Another discussion, nice! More info on slides. 

### Processes in Linux
1.`ps -a` -- see *all* processes some other flags which do things. 
has `-aux` flag to show all. Lots of other flags, etc.
`ps -A -o #cpu`. `ps` computs CPU precentege relative to the amount of time a process exists. In `htop` we compute it during the refresh rate 
    
2.`jobs` -- all localy valalabile processes 
3.`fg`
4.`top` -- interactive ps commands `htop` -- fancier versopn with faster update etc. 
5. `tty` which terminal you are in 
6. Search for a process `ps -*PROCESS_ID*` to seach by name `ps -C *PROCESS_NAME*` or `ps -A|grep bash`

if tty is marked `?` because they are talking to the real terminal and we are only talking to the fake terminal.

### Hard Links vs Soft (Symbolic) Links 
*Hard link* refrences the physical location of the file, it's just as valid as the previous refrence to that file. A *soft link* just contains a text refrencign the address of some hard link to the physical location. 
-- you already have the syntax from the previous lecture. 
`ls -l` allows you to see the pointers of a the soft links.
if starts with `l` when you `ls` in the premission is a symbolic link

---------
*example:* make file *a* create symbolic link and hard link, delete *a*, hardlink is still accesible softlink is not. 


-------
when doing `ls` green is executable and blue is symbolic link 

### chmod 
Can change with `+r -r` etc or `777` or any other 3 deci combination to specify the 3 bits 

## Shell Scripting!
Mandatory first line
special keywords for stuff
syntax for looping and conditionals
must change premisions to `+x` to enable execution 
spaces are important between the commands 


### Random 
1. `wc FILENAME` to get number information for a file, number of lines, words, charecters 
2. SOME COMMAND `2> err.txt` output error into a file. 
3. COMMAND `|` COMMAND_2 use output of COMMAND for input of COMMAND_2

### Questions
1. what does ? `tty` mean? 
2. what's `pgrep` again?
3. try to `cat` a symbolic link, do you `cat` the file? 


project signups! 