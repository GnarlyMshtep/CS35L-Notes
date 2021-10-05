topics for today 
    - indirection 
    - layering 
    - pattern matching 
"any problem in CS can be solved with another level of indirection" 


##### from last time 

### Indirection 
give you illusion through level of abstraction -- the ideas of levels of abstraction over level of abstraction, etc. 
instead of pointing to what you want, point to something that points to what you want, secondary pointers. 

### inode numbers -- file sequence number
There's a unique integer associated with every file in the system. 
so `ln` uses that 
functions map names to file numbers (numbers) (partial from $\Sigma{}^*$-> N) 
directories are files too, every directory has a number 

### Symbolic link
a 3rd kind of file (Other than directories and reg files). 
a small file containing a string
can create a symbolic link by any name
always interperted in the context they are in. for example moving `..` link will change where we are from. only / is absolute  

have /usr/bin and /bin, link them to each other! 
`ls -l /bin` -- using for backwaords compatibillity
only gets resulved and checked when you try to use them as preceding in a file
you would use hard link over symbolic link if you want to make sure you don't lose the file. So if you someone moves the origin file, you lose it if you only have a symbolic refrence. 
a symbolic link is always interperted in the context that it used. so hard link to symbolic link will be considered in the hard links context. 

to resolve symbolic link infinite loops, 20 is the stack overhead. u can build this loops with `ls -l e`.  


``
[matan@lnxsrv10 ~/Documents]$ ls -l
total 0
lrwxrwxrwx 1 matan csugrad 2 Sep 30 14:23 parent -> ..
[matan@lnxsrv10 ~/Documents]$ cd parent
[matan@lnxsrv10 ~/Documents/parent]$ ls
#1_5_answers.txt#  assign1.html  Downloads   exer2.html   exer3.html   exer4.html                               helloWorld.txt  locale       UsersmatanDesktop
1_5_answers.txt    Desktop       exer1.html  exer2.html~  exer3.html~  exer4.html~                              lab1.drib       perl5
a.out              Documents     exer2.diff  exer3.diff   exer4.diff   google-chrome-stable_current_x86_64.rpm  lab2.drib       #tryit.txt#

### Ouuu a new file type 
`ls -l` /dev/null /dev/full` -- have a c, are charecter special file whatever


### A bit about reg files
finite sequence of bytes (8 bits) -- it doesn;t contain it's metadata (info, size, etc).
metainfo 
    - timestamps (read/write, last access, creation)
    - ownership/group
    - link count 
    - permissions
###### Permissions
12-bit ints, all that ur allowed to do with a file and not allowed to do. 
4 3 bit pieces (users ,users in your group,other users)
in each group -- (r,w,x) on means yes, no means no, 
*3 initial bits*

### representing large charecter set using byte strings unicode
UTF-8 everything is still in byte-codes to emulate all the characters in unicode. 
how UTF 8 works *[0, payload_7_bits]* (1), but what if u don;t fit in 7 bit? 
[1,1,p ,pay][1,0,load]
[1,1,1,0,payload]
first bits tell you how long the chrecter is, the trailing bytes always start 1,0
allows you to do *semi-random* access and know when you are at the start of the string
a special case is when you have a single byte where we just have what's tagged (1) above. 
if your byte starts with `10` you know you're in the middle of the charecter. 

### Emacs Terminology
- file (like POSIX)
- buffer -- string of bytes in emac's RAM, most buffers are associated with files, no every buffer has a file and not every file has a buffer
- *window*: view of (a part of) a buffer. 
- *frame* what most people would call a window.  `C-x 5 2` -- like a diffrent instance of emacs 

**emacs shell is not interactive, just directing input and output from the actual terminal into emacs**

### Directory editing mode, layering, and modes
what you type is depended on the modes you are in. 

### When your'e editing file F in emacs
- .#F symlink to user editing the file, the user, the machine we are editing on, 
- F~ a copy of F before you have started editing it. 


#### random commands and such
1. `ls -li` show the file numbers
2. `ls -ali` same as above, but also has directories. 
3. can't do `ln` as indirection -- **you cannot create hard links to directories** except for built in one's ('..' and '.')
    - we do that to avoid the links to directories to avoid loops which would cause infinite recursive structure. 
4. `ln -s TO FROM`  (source, target) create a symbolic link from FROM to TO. 
5. `ln -s ooofooof no-such-file` -- symbolic link from no such file to ooofooof, 
6. in emacs `C-x =` tells you what charecter you are looking at, like the hex which make it up
7. `C-u C-x =` is same as above, just more detail
8. `C-x 5 2`-- start a new frame
9. `C-x 5 0`-- delete frame
10. `C-x d` -- directory editing buffer
11. `C-/` -- better than `C-u`
12. `M-!` to run a command directly 
13. `cd .snapshot` Only on SEASnet, a snapshot of OS files for like a week. -- NetApp file WAFL






### Questions 
1. how are directories and files differ? How are they the same? etc, whats the diffrence
2. role of initial /
3. floyds slow and fast pointers
4. the first 3 bits 
5. what is `od` again? 
6. `man emacs client`
7. are directories files

