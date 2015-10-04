<h1>D-CTF</h1>
<h2>crypto 300 - Custom function engineering</h2>

Le but de cette épreuve était de faire la cryptanalyse de la fonction suivante : 

```php
<?php

function xorIt($charOne, $charTwo)
{
	return $charOne ^ $charTwo;
}

function asciiValue($char)
{
	return ord($char);
}

function encrypt($plainText)
{
	$length = strlen($plainText);
	$space = 0xA;
	$cipherText = "";

	for ($i = 0; $i < $length; $i++) {
		if ($i + $space < $length - 1) {
			$cipherText .= xorIt($plainText[$i], $plainText[$i + $space]);
		} else {
			$cipherText .= xorIt($plainText[$i], $plainText[$space]);
		}

		$space = (asciiValue($plainText[$i]) % 2 == 0 ? $space + 1 : $space - 1);
	}

	return bin2hex($cipherText);
}

?>
```

Un message encrypté qu'il fallait décryper était joint au sujet : 

```
320b1c5900180a034c74441819004557415b0e0d1a316918011845524147384f5700264f48091e4500110e41030d1203460b1d0752150411541b455741520544111d0000131e0159110f0c16451b0f1c4a74120a170d460e13001e120a1106431e0c1c0a0a1017135a4e381b16530f330006411953664334593654114e114c09532f271c490630110e0b0b
```

L'algorithme consiste à xor chacun des bytes du message avec un autre situé plus loin dans le plaintext.
L'espacement (SPACE) entre les deux caractères est variable. Initialement, il est de 10 mais il augmente ou diminue selon que le byte traité i est pair ou impair.

On peut noter que si un byte est impair, le suivant sera chiffré avec la même clé : 

Soit A le message chiffré et a le message en clair :

Si a[0] est impair alors :

```
A[0] = a[0] ^ a[0+10]
A[1] = a[0] ^ a[1+9]
```

Or, connaissant, le message chiffré :  

```
a[0] = A[0] ^ a[0+10]
a[1] = A[1] ^ a[1+9]
```

Définissons un set de caractères (CHARSET) possibles pour chacun des caractères du message en clair. Pour commencer j'ai utilisé les caractères dont le code ascii est entre 0x20 et 0x7E.
A l'initialisation, 95 il y a donc possibles pour chaque byte du message chiffré.

Testons maintenant tous les caractères (CHAR) possibles pour a[i+SPACE] et voyons si ```a[i+SPACE] ^ A[i] = a[i] avec a[i] inclu dans CHARSET```
Tous les bytes qui ne satisfont pas cette règle ne sont pas possibles. Le nombre de possibilités pour a[i+10] diminue. 

De plus, ont peut catégoriser les CHAR qui la satisfont en deux groupes : ceux qui ont un résultat pair et ceux qui en ont un impair. 
Nous avons donc deux set de possibilités pour a[i+SPACE], un (PAIR) qui donnera a[i] pair et l'autre (IMPAIR) a[i] impair.

On peut alors considérer arbitrairement que le résultat est impair et continuer notre routine avec ```i = i+1 et SPACE = SPACE-1```.
Quand un set de solution pour a[i+SPACE] est vide, alors on sait qu'aucune possibilité n'existe et que nous avont fait fausse route. Par backtracking, nous revenont alors en arrière pour choisir le set de possibilités IMPAIR.


```python
import copy

cs = [0x20] + range(0x21, 0x30) + range(0x30,0x3a) + range(0x41,0x5b) + [0x5F] + range(0x61, 0x7b) + [0x7b, 0x7d]
charset = [chr(x) for x in cs]

def matchFilter(l1, l2):
	ret = []
	for x in l1:
		if x in l2:
			ret.append(x)
	return ret

def bruteforce(cypherText, i = 0, space = 0xA, mask = None, parity = None, depth = 0):
	# init : 1st iteration	
	if mask == None:
		mask = [charset for k in range(len(cypherText))]
	
	if parity == None:
		parity = [None for k in range(len(cypherText))]

	# candidate solution found
	if i==len(cypherText):
		# arbitrary : doing 5 rows to have the fewest candidates possible.
		# infinite loop, so we return False and continue to look for other candidates (there must be only one but a wide charset may bring false positive)
		if depth > 5*len(cypherText):
			print mask
			return
		i = 0
		space = 0xA		
	
	# for test only
	if space >= len(cypherText):
		return False

	oddSecondCharList = []
	oddFirstCharList = []
	evenSecondCharList = []
	evenFirstCharList = []
	
	secondCharPos = i + space
	if secondCharPos >= len(cypherText)-1:
		secondCharPos = space
	
	for keyCharCandidate in mask[secondCharPos]:
		charCandidate = chr(ord(cypherText[i]) ^ ord(keyCharCandidate))
		if charCandidate in mask[i]:
			if ord(charCandidate) % 2 == 1:
				oddSecondCharList.append(keyCharCandidate)
				oddFirstCharList.append(charCandidate)
			else:
				evenSecondCharList.append(keyCharCandidate)
				evenFirstCharList.append(charCandidate)

	reducedOddSecondCharList = matchFilter(oddSecondCharList, mask[secondCharPos])
	reducedOddFirstCharList = matchFilter(oddFirstCharList, mask[i])
	if parity[i] == None or parity[i] == "odd":
		if len(reducedOddSecondCharList) > 0 and len(reducedOddFirstCharList) > 0:
			mask_copy = copy.deepcopy(mask)
			mask_copy[secondCharPos] = reducedOddSecondCharList
			mask_copy[i] = reducedOddFirstCharList
			parity_copy = copy.deepcopy(parity)
			parity_copy[i] = "odd"
			ret = bruteforce(cypherText, i+1, space-1, mask_copy, parity_copy, depth+1)

	reducedEvenSecondCharList = matchFilter(evenSecondCharList, mask[secondCharPos])
	reducedEvenFirstCharList = matchFilter(mask[i], evenFirstCharList)
	if parity[i] == None or parity[i] == "even":
		if len(reducedEvenSecondCharList) > 0 and len(reducedEvenFirstCharList) > 0:
			mask_copy = copy.deepcopy(mask)
			mask_copy[secondCharPos] = reducedEvenSecondCharList
			mask_copy[i] = reducedEvenFirstCharList
			parity_copy = copy.deepcopy(parity)
			parity_copy[i] = "even"
			bruteforce(cypherText, i+1, space+1, mask_copy, parity_copy, depth+1)	
	return	
```


Pour un CHARSET de 0x20 à 0x7F, le nombre de possibilités pour chaque caractère est trop important pour pouvoir le deviner.
En réduisant le CHARSET, on obtient sois aucun résultat si on a supprimé un caractère présent dans le message en clair, soit un nombre de possibilités par caractère plus resteint.
Avec celui en exemple, le message clair devient visible : 



```
V????well d????you CT? pwner. Now I have to give you the reward for all this hard work or maybe guessing. The flag is?c?y?tanalys?s_is_hard
```

