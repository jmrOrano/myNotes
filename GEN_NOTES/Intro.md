### Feb21
- Installed Obsidian 
- Naka save ang vault sa portable storage para compact.
#### Practical Marking Down Cheat Sheet 
1.  <!-- # Header1Title --> : For Header 1 to 6.
	1. <!-- # H1  to  ###### H6 --> 
2. <!--  - Item 1 --> : For Bullet Item 
3. <!--  - [space] --> : For Todo List

#### For Text Formatting
1. <!-- **Bold** -->
2. <!-- *italic* -->
3. <!-- ***bold + italic***-->
4. <!-- ~~strikethrough~~ -->
5. <!-- ==MarksAsImportant  --> 
6. <!-- `*word`* --> :  for *`documentation`* style noting | For *`commandNames`*  
7. <!-- **`word`** --> : same sa number 6 pero naka bold instead na italize
8. <!-- `word` --> : Usually sapat na for `emphasis`
9. <!--      ```  --> to enable console
#### (WikiLinks) For Links & References  
1.  <!-- [[NoteGoesHere]] -->  : For lingking notes/referencing         
2.  <!-- [Google](https://linkgoesheredotcom) --> : External Link
3. <!-- [[File.md#Header]] --> : Link the header into another file (For expanded explanation)

#### Blockquotes
***Great for highlighting important points or references
1. <!--  > This is quote or important note -->
2. <!--  >> For nesting the quotes -->

#### Callouts
<!--
    >[!Note]  --- General use  
    
	>[!Info]  --- for details and clarification
	>
	>[!Todo] --- for Tasks or action items
	>
	>[!Example] --- for illustration or sample context
	>
	>[!Bug]--- Tecnicl issue or glitches
	>
	>FOR SUMMARIES OR OVERVIEWS
	>[!Abstract]
	>[!Summary]
	>[!tldr]
	>
	>FOR ALERTS OR THINGS TO WATCH OUT FOR
	>[!Warning]
	>[!Caution]
	>[!Attention]
	>
	>FOR ADVICE OR BEST PRACTICE
	>[!Important]
	>[!Hint]
	>[!Tip]
	>
	>FOR HIGHLIGHTED QUOTATION
	>[!Quote]
	>[!Cite]
	>
	>Tip : adding a dash - for collapsible note [!Example]- Collapsible Note
	>Tip: A callout can be nested example shown below
	>
	>[!Note] Main Note
	>This is the outer callout
	>
	>>[!Tip] Nested Tip
	>>This is a callout inside another callout
-->


---
### FOR STANDARD MARKDOWN
```
Go to settings > Files and Links
Turn of the toggle for  USE [[WIKILINKS]]
OPtional :Set NEw Link format to Relative path to file for maximum compatibilty with other editors
```
#### (Standard) For Linking $ Reference
*If the file or header has a space. Replaces the space with the symbol : `%`*

`[Title](File.md)`                —> LInk to another file in the vault
`[Title](Folder/File.md)`   —> If its in another folder
`[Title](File.md#Heading)` —> Link to specific heading
`[Title](https://Link)`       —> LInk to external website


#### CALLOUTS
*In github these are the ones that can work*
```
NOTE
TIP
IMPORTANT
WARNING
CAUTION
```


#### Horizontal Rule
1. <!-- ---  --> : For visual separation

##### Tables
<!--       |Column 1 | Column 2 | -->
<!--       |:---:                                    --> 




---
##### Zettelkasten Style
>***gfgfgfmethod of taking a note ---- google the rest


**TODO  : TEMP : FEB 22 
- [x] Learning using Git and Github
- [x] Implement sa Obsidian
- [ ] Install Niri
- [ ] Continue learning sa Labex Linux Journey


**TODO : LEARN CONCEPS : FEB 26**
- [ ] What is the `syslog` file in *`/var/log/syslog`*


---
#### MARCH

**WALLPAPERS**
[Repo](https://github.com/dharmx/walls)]

---
#### Website I go to.
*June 20, 2026*
**FOR PURE LINUX FUNDAMENTALS**
`Labex.io` — for linux fundamentals
`overthewire.org/wargames/bandit` — Linux survival skills 

**FOR WEB APPLICATION HACKING**
`PortSwigger Web Acaademy` — The gold standard for web exploitation like `SQLi`, `XSS` `CSRF`, `Authentication flaws`, `IDOR`, `SSRF`. GOOD FOR WEB SECURITY
`HackingHub` — Similar to Potswigger but smaller 

**FOR BROAD CYBERSEC + ETHICAL HACKING**
`TryHackMe` — The most balanced platform. Covers `Networking`, `Linux`, `Web`, `Basic Exploitation`, `Blue team`.  BEST STARTING POINT BEFORE HACK THE BOX.

`HackTheBox` — Real pentesting mindset. More authentic. Rewarding but frustrating if fundamentals arent solid yet.

```THE_PLAN
Finish Bandit → 
Start TryHackMe → 
PortSwigger when web clicks → 
HTB when you're comfortable.

 Don't open too many at once — same trap as your notes.
```


---
Excel formula for making a lookup table to practice [[Commands#^23b272|rot13]]
```
=sequence(26,,65) 

Then change it
=char)sequence(26,,65)

# 65 IS THE ASCII VALUE OF A 
# 97 IS FOR THE LOWER CASE
```


---
## Initial Quote PC build 
July 02, 2026


Budget

| Parts   | Model                                                 | Price          |                                                                                                                                                                                                                                                                                  |
| :------ | ----------------------------------------------------- | -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CPU     | R5 5600 (AM4, 6C/12T, no iGPU)<br>R5 5600X            | 7,053<br>8,100 | [EasyPC](https://easypc.com.ph/products/amd-ryzen-5-5600-socket-am4-3-5ghz-processor-with-wraith-stealth-cooler-mpk)<br>[EasyPC](https://easypc.com.ph/products/amd-ryzen-5-5600x-socket-am4-3-7ghz-processor-with-wraith-stealth-cooler-mpk-vr-ready-premium-desktop-processor) |
| Mobo    | ASRock A520M-HVS (AM4,DDR4)<br>Gigabyte B550M DS3H R2 | 3,250<br>5,150 | [DataBlitz](https://ecommerce.datablitz.com.ph/products/asrock-a520m-hvs-amd-socket-am4-ddr4-motherboard)<br>[GameOneStore](https://gameone.ph/gigabyte-amd-b550m-ds3h-r2-amd-socket-am4-ultra-durable-motherboard.html)                                                         |
| RAM     | 16GB (2x*) DDR4-3200 (G.Skill Ripjaws V or equiv.)    | 9,195          | [GameOnePh](https://gameone.ph/g-skill-ripjaws-v-series-16gb-2x8gb-288-pin-sdram-pc4-25600-ddr4-3200-f4-3200c16d-16gvkb.html)                                                                                                                                                    |
| Storage | 500GB NVMe SSD Gen4 (Kingston NV3 / MSI Spatium M450) | 6,900          | [PCExpresss](https://pcx.com.ph/products/kingston-500gb-nv3-m-2-pcie-nvme-solid-state-drive)                                                                                                                                                                                     |
| GPU     | ASRock RX 6600 8GB Challenger D                       | 14,500         |                                                                                                                                                                                                                                                                                  |
| PSU     | 550-650W 80+ Bronze (RAKK AGOS 650W or similar)       | 1,800 - 2,800  |                                                                                                                                                                                                                                                                                  |
| Case    | Budget mATX/ATX case with fans                        | 1,750          |                                                                                                                                                                                                                                                                                  |
*Upgrade roadamp*

Slightly expensive(focusing both gaming and running AI locally)

|  Parts  | Model                                                 | Price  |                                                                                                                                         |
| :-----: | ----------------------------------------------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------- |
|   CPU   | R5 5600 (AM4, 6C/12T, no iGPU)                        | 7,053  | [EasyPC](https://easypc.com.ph/products/amd-ryzen-5-5600-socket-am4-3-5ghz-processor-with-wraith-stealth-cooler-mpk)                    |
|  Mobo   | # Gigabyte B550M DS3H R2                              | 5,150  | [Datablitz](https://ecommerce.datablitz.com.ph/products/gigabyte-b550m-ds3h-r2-ultra-durable-motherboard)                               |
|   RAM   | 16GB (2x*) DDR4-3200 (G.Skill Ripjaws V or equiv.)    | 9,195  | [GameOnePh](https://gameone.ph/g-skill-ripjaws-v-series-16gb-2x8gb-288-pin-sdram-pc4-25600-ddr4-3200-f4-3200c16d-16gvkb.html)           |
| Storage | 500GB NVMe SSD Gen4 (Kingston NV3 / MSI Spatium M450) | 6,900  | [PCExpresss](https://pcx.com.ph/products/kingston-500gb-nv3-m-2-pcie-nvme-solid-state-drive)                                            |
|   GPU   | Rx 7600XT 16GB DDR                                    | 26,500 | [EasyPC](https://easypc.com.ph/products/asus-dual-rx-7600-xt-dual-rx7600xt-o16g-oc-16gb-128-bit-gddr6-videocard?variant=43909964988587) |
|   PSU   | Cooler Master MWE 650W and 650W V2 80+ bronze         | 3350   | [EasyPC](https://easypc.com.ph/products/coolermaster-mwe-650w-80-bronze-power-supply?variant=45295690317995)                            |
|  Case   | # Montech Air 100 Lite Mesh TG Micro-ATX Case         | 2295   | [Datablitz](https://ecommerce.datablitz.com.ph/products/montech-air-100-lite-mesh-tg-micro-atx-case)                                    |
