# Enumeration of username

Description
------------
Tools for generating usernames when penetration testing. *Usernames are half the password brute force problem.*

This is useful for user account/password brute force guessing and username enumeration when usernames are based on the users' names. By attempting a few weak passwords across a large set of user accounts, user account lockout thresholds can be avoided.

Users' names can be identified through a variety of methods:
* Web scraping employee names from LinkedIn, Facebook, and other social networks.
* Extracting metadata from document types such as PDF, Word, Excel, etc. This can be performed with FOCA.
link: https://github.com/urbanadventurer/username-anarchy
We can use a tool such as [Username Anarchy](https://github.com/urbanadventurer/username-anarchy) to create common username permutations based
on the full names. After saving the full names to a text file, we run the script.
Here is a txt file with full names. Then you can run username-anarchy to scamble the username

Example Usage
-------------
### You know the name of a user but not the username format

	./username-anarchy anna key
	anna
	annakey
	anna.key
	annakey
	annak
	a.key
	akey
	kanna
	k.anna
	...


### You know the username format and names of users

	./username-anarchy --input-file ./test-names.txt  --select-format first.last
	andrew.horton
	jim.vongrippenvud
	peter.otoole


### You know the server is in France
Note that -a or --auto is required when you do not specify any input names.

	./username-anarchy --country france --auto
	martin
	bernard
	thomas
	durand
	richard
	robert
	petit
	moreau
	dubois
	simon
	martinsmith
	martinjohnson
	...

### List username format plugins

	./username-anarchy --list-formats
	Plugin name         	Example
	--------------------------------------------------------------------------------
	first               	anna
	firstlast           	annakey
	first.last          	anna.key
	firstlast[8]        	annakey
	firstl              	annak
	f.last              	a.key
	flast               	akey
	lfirst              	kanna
	l.first             	k.anna
	lastf               	keya
	last                	key
	last.f              	key.a
	last.first          	key.anna
	FLast               	AKey
	first1              	anna0,anna1,anna2
	fl                  	ak
	fmlast              	abkey
	firstmiddlelast     	annaboomkey
	fml                 	abk
	FL                  	AK
	FirstLast           	AnnaKey
	First.Last          	Anna.Key
	Last                	Key
	FML                 	ABK
