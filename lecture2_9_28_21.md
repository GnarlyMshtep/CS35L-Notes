Today: 
- POSIX File System 
- POSIX Shell 
- EMACS

been around for a while, and may remian because it's a core building block


# POSIX 
Portable operating system IX spec 1980's, maintained by IEEE -- spec for all but windows
we will read the core. 

## POSIX Shell
modes: 
1.  operates like many interpertesrs in a read-eval-print loop. (REPL)
2. script (usully a file).

## how is emacs built ontop of those models? 
*files* -- live is fs and are presistent, they are on disk, flash, etc. 
*buffers* -- on Emac's RAM -- preformence advantages of RAM over disk, disk wear-out -- differ from what you are working for an is not permennent (version control essentially, don't change all the time, only when sure, save extra copies).  
    essentially array of text in the C level. 

- many buffers are not associated with files and many buffers are not associated to files. 
    you can have 2 buffers for the same file, because "you have 2 opinions of what the file ought to look like"
    2 files 1 buffer, -- nope, simillar reasons. 


## POSIX file system 
Tree structure (almost) starting with a root. 
**directory** -- partial function from *file name components*--strings with no slashes or nullbytes to files (binary strings)
    - nice, gotta love formalizations
root directory- first directory, top dog, called */*. can think of navigation down as a composition of partial functions, nice! 
every directory has 2 special entries 
1. *.* points to the directory itself
2. *..* points to its parent -- root is it's own parent 
- can have multiple parents for file using symbolic links 
    how? `ln A B` create a symbolic link B for the existing file A 
    column 2 number for `ls -l` is the number of names for this file? -- link cap 

## random commands and whatever
### Shell

1. `sh *scriptFile*` run the shell from the shell, where scriptFile is 
2. feed output of left with right `|`
    `echo echo solong | sh` prints only solong, figure out why. 
3. `jobs` command to your current shell instance only instances that belong to it. -- a shell builtin 
4. `ps` look anybody's processes. -e means all in system  
    bash-4.2$ ps
    PID TTY          TIME CMD
    18386 pts/9    00:00:00 bash
    18932 pts/9    00:00:00 ps
    from EMACS, pnly the ps command and bash 
5. `fg` resume the last commands  
6. `type` sort of like `which`, just throught the hash table, can recognize when something is a builtin 
7. `pwd` stands for present working directory!

### EMACS
3. double vs. double can have fancy expressions, single -- what u see is what u get. 
4. `C-x o` other buffer
5. `C-x C-b` list buffer
6. `C-x 0` Please stop displaying the buffer
7. `C-g` stop the command I am currently doin
8. `C-z` suspend EMACS from doing stuff in OS. not as useful anymore. 
9. `C-x C-c` exit Emacs
10. `C-x` b NAME RET switches to a buffer with a name NAME, makes that buffer if it does not exist
11 `C-x 4b` same as 10. but in another window
12. `C-x C-s` Copy buffers content into that file, i.e. save. 
13. `C-h` general help 
13. `C-h C-h` help with help
14. `C-h COMMAND` what does COMMAND do in great detail
15. `C-h i` all info for the entire system 
16. `C-h t` tutorial 
17. `C-s` search forword `C-r` search backwards 
18. TAB -- used a lot for completion 
19. `M-w` all between mark 
20. `C-y` yank 
21. `M-q` buetify/indent/reformat 
22. `ln rm mv` don't actually delete the files, just shuffle the pointers around. 
    so `rm` only removes the name for the file 
    ` (rm p; sleep 3; tail -n 2)< p -- read from a file that has been removed -- 
23. `sleep SECONDS` wait for 10 seconds 
24 `shred NAME` repeatedly overwrites the file and then removes the file (think about it! ussuly stuff is in disk   ) 


## Questions!
So how do you **actually** delete a file? 





