This is a Level 2 problem. The prompt is as follows:

> We found sorandom.py running at shell2017.picoctf.com:27691. It seems to be outputting the flag but randomizing all the characters first. Is there anyway to get back the original flag?
>
> HINTS:
> How random can computers be?

The hint did not prove to be that helpful for this problem, as pseudorandom numbers are not a well kept secret. The first order of business was to connect to the given port and obtain the randomized flag, as shown below:

```
	[on home system]
	$ netcat shell2017.picoctf.com 27691
	Unguessably Randomized Flag: BNZQ:20380043pc5p8u861tcy650q8xn8mf5d
```

The given address is a hostname and a port, which makes the netcat command (alternatively, nc) the right tool for the job. The output was as promised, a randomized version of the flag. Now it was time to look under the hood at the script that was generating this randomized flag. The code for sorandom.py is as follows:

```
	#!/usr/bin/python -u
	import random,string

	flag = "FLAG:"+open("flag", "r").read()[:-1]
	encflag = ""
	random.seed("random")
	for c in flag:
	  if c.islower():
	    #rotate number around alphabet a random amount
	    encflag += chr((ord(c)-ord('a')+random.randrange(0,26))%26 + ord('a'))
	  elif c.isupper():
	    encflag += chr((ord(c)-ord('A')+random.randrange(0,26))%26 + ord('A'))
	  elif c.isdigit():
	    encflag += chr((ord(c)-ord('0')+random.randrange(0,10))%10 + ord('0'))
	  else:
	    encflag += c
	print "Unguessably Randomized Flag: "+encflag
```
It looks like the script is reading the raw flag from some file, then iterating over it character by character. Looking at the body of the main loop and the helpful comment therein, it looks like the script is doing a modular operation on each character of the flag based on a randomly generated number to obtain a new character. One huge clue is that the aforementioned random number is not actually random at all, because it is being generated from the same seed (“random”) every time. The nature of this modular operation is that it can be easily reversed. I started by replacing the flag variable with the script’s scrambled output, BNZQ:20380043pc5p8u861tcy650q8xn8mf5d. Upon running the modified script, the output was: 

```
	XPYA:54561966de2b5m888mzs016e3sx5uj6e
```

However, the first 4 characters did not spell ‘FLAG’, and the Pico answer box confirmed this was not the desired output. This stumped me for a while, but then I realized that I was not completely reversing the randomization process. The script needs to subtract whatever randomized modular value was added to every character to completely reverse the scrambling process. The code for the complete anti-sorandom.py script is as follows:

```
	#!/usr/bin/python -u
	import random,string,sys

	flag = sys.argv[1]
	encflag = ""
	random.seed("random")
	for c in flag:
	  if c.islower():
	    #rotate number around alphabet a random amount
	    encflag += chr((ord(c)-ord('a')-random.randrange(0,26))%26 + ord('a'))
	  elif c.isupper():
	    encflag += chr((ord(c)-ord('A')-random.randrange(0,26))%26 + ord('A'))
	  elif c.isdigit():
	    encflag += chr((ord(c)-ord('0')-random.randrange(0,10))%10 + ord('0'))
	  else:
	    encflag += c
	print "Unguessably Randomized Flag: "+encflag
```

Upon feeding this anti-script the randomized flag, we can get the original, as shown below:

```
	[on home system]
	$ python2 sorandom.py "BNZQ:20380043pc5p8u861tcy650q8xn8mf5d"
	Unguessably Randomized Flag: FLAG:96109120ba8d1c844afe294c3cd1eb4c
```

I fed the encrypted flag into the anti-script as a command line argument because the system clipboard was not being cooperative when pasting into the text editor itself. I enjoyed this challenge because it allowed me to dust off some python skills, and draw upon some knowledge of substitution ciphers and modular encryption from CSE 4471.

