use h3 for date and h4 for topics
### |Feb 21
#### Ano ang difference ng Terminal vs Shell? 
	Isipin mo na ang Terminal ay isang application na similar sa mga GUI apps. Pero, sa halip na Graphical User Interface, ang Terminal ay Text Base Interface.
	
		Halimbawa. : Sa pag hanap ng IP Address
		GUI      -> Setting -> Wifi Setting -> WifiNameInformation
		Terminal -> ipconfig or ifconfig

	Ang SHELL naman ang command interpreter. Ito ang gumagawa ng mga iutos mo bilang user.
	Ito ang middle man para makausap mo ang core OS/kernel.
	Summary/Note : Ang Shell ay mag rurun parin kahit GUI ang gamit mo dahil nagawa parin ito ng mga commands. 
		TERMINAL: is a tool
			Ex: GNOME Terminal, Konsole
		SHELL :  the middle to OS/Kernel
			Ex: Bash, Zsh
**Note: Ang Terminal command as we know tulad ng GNOME Terminal, Konsole ay mga ***Terminal Emulator lang din*** na nag eemulate ng **[[Feynman-Technique#TTY: Teletype Writer|Teletype Writer]]

___

### |Feb 22
#### TTY Teletype Writer
----Isang Terminal base interface nag nagbibigay ng way para magamit mo ang comptuter ng hindi na dumadaan sa GUI ng Desktop

**Pano Gamitin?**
**`Ctrl + Alt F1....F6`**
`login: username
`password: <!-- ******* --> 
**`$ls`**
<!-- Display the list of contents -->

**Kailan ginagamit?**
- It manage servers without graphics
- Rescue system when GUI fails, since this one runs on system level
- Lightweight and fast (*Good for Admin, system recovery, scripting, and low resource env*)


#### Version Control
----Napaka useful method/system para ma revert ang state ng isang project kung sakaling may mistake or undesireable changes.

**Practical Example of How it works | But no Git, full manual**
----Imagine mo may project ka na nasa thousands ang bilang ng files na nasa multiple directories. Then as you progress, marami ka nang nabago then biglang may mga error/mistake ka na nagawa and hindi mo na alam ayusin. 
---Buti na nalang may nag create ka ng copy ng buong folder ng project mo so you can restore it. *But this can be stressful and impractical kung ang laki ng size ng project mo*
----**Buti na lang may [[Feynman-Technique#Version Control System But with Git| Version Control System]] ka.

###### Version Control System :  But with Git
----Sa pag gamit ng tamang tools for Version Control System like ***Git***. Mas efficient dahil : 
- Hindi mo na need mag create ng multiple copies ng project. 
- With git, nag lo-log lang ito ng mga pag babago and differences sa files
- Not only this allows to have multiple versions throughout the progress ng project mo. Ma aallow pa nito ang collaborations.
- At also may dagdag feature na [[Feynman-Technique#Branch and Merging in Git| Branch and Merging]]

###### Branch and Merging in Git
----pwede ka gumawa ng mga experimento/possible outcome ng project or bagong features, tapos isasam/*merge* sa main branch ng project pag okay na. 
-----Hindi nito na aapektuhan ang main branch ng project ( karaniwang ang *main* o *master*)

----

### Feb 23
#### Bakit isinasama ang .gitignore sa git add. ? 
-  Ang **`.gitignore`** ay isang _configuration file_ na nagsasabi kay Git kung aling mga files/folders ang hindi dapat i-track.
- Ang **`.gitignore`** ay hindi lang basta lagayan ng mga ignored files for **`git add.`** . Ito rin ang mag sisilbing automatic rules para sa mga collaborators. To avoid accidentally commit ng build files or secrets 
- Ang **`.gitignore`** file ay: 
	- Hindi personal preferences
	- Hindi system level config
	- Bahagi ito ng strucutre ng project  


### FEB 25
#### ANO ANG SYMBOLIC LINKS? 
`Shortcut(.Ink file)`
***Symbolic link*** ay isang shortcut file pero mas low level 

Madalas gamitin sa folders or files.
##### Core Idea
Kapag gumawa ka ng symbolic link:
- Hindi nito kinokopya ang file
- Hindi ito duplicate
- Isa lang itong “pointer” papunta sa original file
###### Example
Original File:
***`/home/userName/Documents/report.txt`***

Creating symbolic link:
***`ln -s /home/orano/Documents/report.txt ~/Desktop/report-link.txt`***

###### Visual Example
`ls -l` Outputs:
**`report-link.txt -> /home/orano/Documents/report.txt`**
Makikita sa arrow (*`->`*) kung nasan yung shortcut 