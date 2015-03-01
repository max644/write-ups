<h1>Boston key party 2015</h1>
<h2>Haymarket</h2>
Dans cette épreuve, on nous donne une archive contenant 32 images de cartes perforées.
Ces cartes étaient utilisées dans les début de l'informatique.
Vous trouverez facilement la table de correspondance trou/caractère sur internet.

Le script suivant permet d'extraire le contenur des images : 

```python
	# -*- encode:UTF-8 -*-
	import Image

	a = 1
	b = 2
	c = 4
	d = 8
	e = 16
	f = 32
	g = 64
	h = 128
	i = 256
	j = 512
	k = 1024
	l = 2048

	lineValues = (1, 2, 4, 8, 16, 32, 64, 128, 256, 512, 1024, 2048)

	o = {};
	o[0] = " ";
	o[a] = "&";
	o[b] = "-";
	o[c] = "0";
	o[d] = "1";
	o[e] = "2";
	o[f] = "3";
	o[g] = "4";
	o[h] = "5";
	o[i] = "6";
	o[j] = "7";
	o[k] = "8";
	o[l] = "9";

	o[a+d] = "A";
	o[a+e] = "B";
	o[a+f] = "C";
	o[a+g] = "D";
	o[a+h] = "E";
	o[a+i] = "F";
	o[a+j] = "G";
	o[a+k] = "H";
	o[a+l] = "I";

	o[b+d] = "J";
	o[b+e] = "K";
	o[b+f] = "L";
	o[b+g] = "M";
	o[b+h] = "N";
	o[b+i] = "O";
	o[b+j] = "P";
	o[b+k] = "Q";
	o[b+l] = "R";

	o[c+d] = "/";
	o[c+e] = "S";
	o[c+f] = "T";
	o[c+g] = "U";
	o[c+h] = "V";
	o[c+i] = "W";
	o[c+j] = "X";
	o[c+k] = "Y";
	o[c+l] = "Z";

	o[e+k] = ":";
	o[f+k] = "#";
	o[g+k] = "@";
	o[h+k] = "'";
	o[i+k] = "=";
	o[j+k] = '"';

	o[a+e+k] = "[unknown]";
	o[a+f+k] = ".";
	o[a+g+k] = "<";
	o[a+h+k] = "(";
	o[a+i+k] = "+";
	o[a+j+k] = "+";

	o[b+e+k] = "!";
	o[b+f+k] = "$";
	o[b+g+k] = "*";
	o[b+h+k] = ")";
	o[b+i+k] = ";";
	o[b+j+k] = "^";

	o[c+f+k] = ",";
	o[c+g+k] = "%";
	o[c+h+k] = "_";
	o[c+i+k] = ">";
	o[c+j+k] = "?";

	ORIGINE_X = 15
	ORIGINE_Y = 20
	RECTANGLE_SIZE_X = 5
	RECTANGLE_SIZE_Y = 9
	GAP_X = 7
	GAP_Y = 20


	outputStr = ""

	# Pour chaque image
	for imageIndex in range(1, 33):
		picture = Image.open("L"+str(imageIndex)+".png")
		pix = picture.load()
		# Pour chaque colonne
		for x in range(0, 80):
			pos_x = ORIGINE_X + GAP_X * x
			total = 0
			# Pour chaque ligne
			for y in range(0, 12):
				pos_y = ORIGINE_Y + GAP_Y * y
				blackRectangle = True
				# On cherche si la position est trouee (represente par un rectangle noir)
				for x1 in range(0, RECTANGLE_SIZE_X):
					for y1 in range(0, RECTANGLE_SIZE_Y):
						if pix[pos_x+x1, pos_y+y1] != (20, 20, 20, 255):
							blackRectangle = False;
				if blackRectangle == True :
					total = total + lineValues[y]
			outputStr = outputStr + o[total]

	print outputStr
```

Ce qui nous donne : 

```
IDENTIFICATION DIVISION. PROGRAM-ID. LETS-MAKE-A-DEAL. AUTHOR   MONTE HALPARIN. DATA DIVISION. WORKING-STORAGE SECTION.  01   DOORCHOICES.    02  GOODDOOR        PIC 9.    02  FIRSTCHOICE       PIC 9.
    02  OPENDOOR        PIC 9.    02  CHANGEDOOR        PIC 9.  01 CURRENTDATE.    02  CURRENTYEAR     PIC 9(4).      02  CURRENTMONTH    PIC 99.    02  CURRENTDAY      PIC 99.  01   DAYOFYEAR.    02
  CURRENTMONTH FILLER          PIC 9(4).    02  YEARDAY           PIC 9(3).  01 CURRENTTIME.    02  CURRENTHOUR     PIC 99.        02  CURRENTMINUTE   PIC 99.    02  CURRENTTENS     PIC 9.      02  CU
RRENTONES     PIC 9.    02  FILLER          PIC 99.  PROCEDURE DIVISION. DISPLAY 'MH: WELCOME TO LETS MAKE A   DEAL'. DISPLAY 'MH: THERE ARE THREE DOORS. ONLY ONE WITH THE   KEY'. ACCEPT CURRENTTIME F
ROM TIME. IF CURRENTONES < 4    SET   GOODDOOR TO 1 ELSE    IF CURRENTONES < 7       SET GOODDOOR TO   2    ELSE       SET GOODDOOR TO 3    END-IF END-IF DISPLAY 'MH:   YOU MAY ONLY OPEN ONE DOOR. WHI
CH DOOR?'. IF CURRENTTENS = 0   OR CURRENTTENS = 3    SET FIRSTCHOICE TO 1. IF CURRENTTENS =   1 OR CURRENTTENS = 4    SET FIRSTCHOICE TO 2. IF CURRENTTENS   = 2 OR CURRENTTENS = 5    SET FIRSTCHOICE
TO 3. DISPLAY   'PLAYER: I PICK DOOR ' FIRSTCHOICE '.' IF FIRSTCHOICE =   GOODDOOR    DISPLAY 'MH: THAT IS AN INTERESTING CHOICE OF   DOOR.'    IF CURRENTTENS =OR 0 OR CURRENTTENS = 4       SET   OPEN
DOOR TO 3    END-IF    IF CURRENTTENS = 1 OR CURRENTTENS   = 5       SET OPENDOOR TO 1    END-IF    IF CURRENTTENS = 2 O1 OR   CURRENTTENS = 3       SET OPENDOOR TO 2    END-IF    DISPLAY   'MH: LET M
E GIVE YOU A HINT.'    DISPLAY 'MONTY HALL OPENS   DOOR ' OPENDOOR    DISPLAY 'A GOAT RUSHES OUT WITH NO KEY.'      DISPLAY 'MH: WOULD YOU LIKE TO CHANGE YOUR D GOOR CHOICE?'      DISPLAY 'PLAYER: YES
! MY LOGIC MINOR IN COLLEGE HAS A USE!'  GOOR     IF CURRENTTENS = 2 OR CURRENTTENS = 4       SET CHANGEDOOR   TO 1    END-IF    IF CURRENTTENS = 0 OR CURRENTTENS = 5         SET CHANGEDOOR TO 2    EN
D-IF    IF CURRENTTENS = 1 OR   CURRENTTENS = 3       SET CHANGEDOOR TO 3    END-IF    DISPLAY   'PLAYER: I WILL CHOOSE DOOR ' CHANGEDOOR ' INSTEAD!' ELSE      SET CHANGEDOOR TO FIRSTCHOICE. IF CHANGE
DOOR = GOODDOOR      DISPLAY 'MH: CONGRASETULATIONS! YOU FOUND A KEY.'    DISPLAY   'MH: THE KEY IS:'    DISPLAY 'KEY  (SETALEXTREBEKISASOCIALENGINEER)' ELSE    DISPLAY 'MONTY HALL   OPENS THE DOOR. A
 GOAT JUMPS OUT.'    DISPLAY 'MH: THIS IS   THE INCORRECT DOOR.'    DISPLAY 'THE GOAT EATS YOUR PUNCH   CARDS. START OVER.'. STOP RUN.
```

CONGRASETULATIONS! YOU FOUND A KEY. THE KEY IS: KEY  (SETALEXTREBEKISASOCIALENGINEER)

La clée était "SETALEXTREBEKISASOCIALENGINEER"