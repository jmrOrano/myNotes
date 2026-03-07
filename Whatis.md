***use H5*** 
##### The .Bashcr file in home dir
*Ito ang user setting config for the terminal.*

Location : *`~/.bashrc or /home/username/.bashrc)

Dito nilalalgay ang [[Whatis#The `env` (environment variables)| env variables]] for users
For customizing prompt
And also mga commands na want i-automate.

---
##### The ~/.profile  /  ~/.xprofile
*Dito nakalagay ang mga script that ***runs at log in*** 
- Any commands na ilagay ko dito ay executed automatically when logged in

---

##### The `env` (environment variables)
A `environment variables` ay *configuration values na available sa shell at sa mga programs habang nag rurun sila*

**Paano ito nagana technically?**
--Kapag nag-run ka ng program:
1. Shell reads the environment variables
2. Then pinapasa niya ito sa program
3. Then program use them for behavior

**Example** 
***`export EDITOR=nano`*** :  kung may program na gumamit ng `$EDITOR`, automatic nano ang gagamtin. But this is only temporary session. 

**TEMPORARY VS PERSISTENT SESSION**
`export MYVAR=hello` : mawawala kapag isinara ang terminal
`export PATH=$PATH:/home/orano/mytools` : this one is automatic every shell session. 