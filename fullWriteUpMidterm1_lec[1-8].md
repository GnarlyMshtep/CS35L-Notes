# Table of Contents
- [POSIX, Unix, Linux (not shell)](#posix--unix--linux--not-shell-)
    + [What are they? Shallow Layeman](#what-are-they--shallow-layeman)
  * [POSIX File System overview](#posix-file-system-overview)
  * [Types of Unix/POSIX Files(ish) Things.](#types-of-unix-posix-files-ish--things)
    + [Regular Files](#regular-files)
    + [File Descriptors](#file-descriptors)
    + [File Premissions](#file-premissions)
    + [Directories](#directories)
    + [Hard Links](#hard-links)
    + [Symbolic links](#symbolic-links)
    + [Special Charecter Files](#special-charecter-files)
    + [Path to file](#path-to-file)
  * [What is Indirection?](#what-is-indirection-)
    + [Inode Numbers (a form of indirection)](#inode-numbers--a-form-of-indirection-)
- [3 Little Languages: (bash) shell (not the programming guide, just overview opf philosophy), Regexp, filenames](#3-little-languages---bash--shell--not-the-programming-guide--just-overview-opf-philosophy---regexp--filenames)
  * [The Bash shell](#the-bash-shell)
    + [The Shell Envieroment](#the-shell-envieroment)
    + [Some random commands and whatsover](#some-random-commands-and-whatsover)
    + [Sed](#sed)
  * [Regular Expressions](#regular-expressions)
    + [Extended RegExp](#extended-regexp)
  * [Filenames](#filenames)
- [Programming in the shell + Commands (all shell syntactical issues)](#programming-in-the-shell---commands--all-shell-syntactical-issues-)
  * [Shell for configuraion](#shell-for-configuraion)
    + [Exit status](#exit-status)
    + [Quoting (more problamatic than you would think!)](#quoting--more-problamatic-than-you-would-think--)
  * [List of `sh` special charecters](#list-of--sh--special-charecters)
    + [The building blocks: Special Tokens and words in bash](#the-building-blocks--special-tokens-and-words-in-bash)
      - [builtins](#builtins)
      - [words](#words)
      - [reserved words](#reserved-words)
      - [Execution blocks](#execution-blocks)
  * [Shell scripting](#shell-scripting)
    + [Ex1: hello world with inputted name](#ex1--hello-world-with-inputted-name)
    + [Ex2 looping and printing special message for odds and evens](#ex2-looping-and-printing-special-message-for-odds-and-evens)
    + [Ex3: args from CMD](#ex3--args-from-cmd)
    + [Ex3 create function](#ex3-create-function)
    + [if then else (conditionals)](#if-then-else--conditionals-)
    + [pattern matching](#pattern-matching)
    + [RandTopic1: Variable substuiton](#randtopic1--variable-substuiton)
    + [randTopic2: Special Vars](#randtopic2--special-vars)
    + [randTopic3: Field Splitting](#randtopic3--field-splitting)
    + [randTopic4: Variable Substuiton](#randtopic4--variable-substuiton)
- [UTF-8](#utf-8)
  * [UTF-8 Representation](#utf-8-representation)
- [Emacs and Lisp](#emacs-and-lisp)
  * [The layers of Emacs](#the-layers-of-emacs)
  * [Emacs Commands](#emacs-commands)
    + [Emacs version control](#emacs-version-control)
  * [Lisp Syntax and Sample programs](#lisp-syntax-and-sample-programs)
    + [sProgram1: new split window](#sprogram1--new-split-window)
    + [sProgram2: middle-of-buffer](#sprogram2--middle-of-buffer)
    + [sProgram3: print of multiple args](#sprogram3--print-of-multiple-args)
  * [Different ways to evaluate lisp in emacs](#different-ways-to-evaluate-lisp-in-emacs)
- [Compilation, Intrepertation, and In-betweens](#compilation--intrepertation--and-in-betweens)
    + [Motivation: Different ways to evaluate lisp in emacs](#motivation--different-ways-to-evaluate-lisp-in-emacs)
  * [Different options when executing code](#different-options-when-executing-code)
- [Modes of Interaction and UI-UX Design](#modes-of-interaction-and-ui-ux-design)
  * [What's a mode](#what-s-a-mode)
- [Python](#python)
  * [Brief History](#brief-history)
  * [Python internals + Mutabillity](#python-internals---mutabillity)
    + [Equality](#equality)
    + [Variables](#variables)
    + [Assign by ref vs val](#assign-by-ref-vs-val)
  * [Python types](#python-types)
    + [Zooming-in; Sequences](#zooming-in--sequences)
      - [Further Zoom; Lists](#further-zoom--lists)
    + [Zoom-In; Dictionaries](#zoom-in--dictionaries)
    + [Callables](#callables)
    + [Namespaces](#namespaces)
    + [Modules & Import](#modules---import)
- [Networks, Internet spec basis](#networks--internet-spec-basis)
  * [Different models of coordination](#different-models-of-coordination)
    + [Client-Server](#client-server)
    + [Peer-to-Peer](#peer-to-peer)
    + [Primary-Secondery computing](#primary-secondery-computing)
  * [New issues for networks](#new-issues-for-networks)
  * [History of Networks](#history-of-networks)
    + [Circuit Switching](#circuit-switching)
    + [Packet Switching](#packet-switching)
  * [Internet Protocol Suite + Layers of Internet (again, the abstraction)](#internet-protocol-suite---layers-of-internet--again--the-abstraction-)
    + [Protocols: networks: APIs: Program](#protocols--networks--apis--program)
    + [Zoom in: Internet Protocol (at the internet level)](#zoom-in--internet-protocol--at-the-internet-level-)
    + [Zoom in: Transport layer](#zoom-in--transport-layer)
  * [The world wide web (WWW)](#the-world-wide-web--www-)
    + [HTTP (1.0)](#http--10-)
    + [HTML](#html)
  * [JS](#js)
- [Questions](#questions)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# POSIX, Unix, Linux (not shell)
### What are they? Shallow Layeman
*Unix*, origanally AT&T Unix, was the original OS developed by Thompson and Ritchie at Bell Labs in the late 60's. The idea behind it can be summerised as "the idea that the power of a system comes more from the relationships among programs than from the programs themselves," and this program-stringing is done using the shell, a thin layer wrapping around the OS. When in doubt, it considers $x$ to be a file. if something is not a file, it is a process. The idea behind "everything is a file" isn't filename, but that you can use common tools (for ex, stdout to write to a file or device) to operate on different things

*POSIX* is a family of general standard issued by the IEEE in the 1988, with an emphasis on files, portabillity (can work on multiple hardware). All but windows at least partially conform to it. 

*Linux* Is a specific Unix type kernal that is a common type of unix. 

## POSIX File System overview
The POSIX file system speifies a tree, made up of (regular) files, directories, hard links, and symbolic links. 
- Tree starts with a root, called `/`, that is, technicaly speaking, it's own parent. 
- a *directory* is a partial mapping from fileName (string with no slashes or nullbytes) to fileLocation. Navigation down the tree can be through of as composition of those partial functions. 

    - every directory contains special entries `.` which point to the directory itself, and `..` which points to the directories parent. 
- A file can have multiple parents using symbolic or hard links.


## Types of Unix/POSIX Files(ish) Things.
https://tldp.org/LDP/intro-linux/html/sect_03_01.html
What are the possible types? 
- "regular" files
- directories
- hard links
- symbolic links
- special charecter files -- /dev/full and /dev/null

### Regular Files
A string of bytes, does not contain its own description (contained a level up at one of its pointers, the inode). 

### File Descriptors
When a file is created, it gets a free inode and some descriptors such as: 
- Owner and group owner of the file.
- File type (regular, directory, ...)
- *Permissions* on the file Section 3.4.1
- Date and time of creation, last read and change.
- Date and time this information has been changed in the inode.
- Number of links to this file (see later in this chapter).
- File size
- An address defining the actual location of the file data.

### File Premissions
12 bit ints, divided into the first 3 bits (setuid, setgid and sticky bits which are ussuly 0), and another 3 groups of 3 bits, specifying **r**ead,**w**rite,e**x**ute premissions for the user who owns the fie, the group that user belongs to, and any other users. (we didn't realy talk about the UNIX user model)

### Directories
A directory is just a files which contains a list entries (filenames) and their IDs (inode #). The ID points to an entry on the inde table, which contains all the meta-information. Again, Inodes are not unique -- two hard links can point to the same inode. Directories also have inode numbers, which are also not unique (think `..` and `.`, but only the OS is allowed to set those, in a disciplained way). 
### Hard Links
A hard link is another name for an existing file on your system. A hard link points to the same inode. It shouldn't even be thought of as an alias-- it is as "valid" as the original file name! Only applies to files, [not directors because worried of infinite looping multiple parents, multiple paths to file.](https://askubuntu.com/questions/210741/why-are-hard-links-not-allowed-for-directories). 

Created with `ln fileToLinkToName hardLinkName.` In the list command, you can see the count of names for the files. Removing the originalName file, the hard link is still avalabile. 

Only within the same file system and partition (becuse of inode # only unique within that context)

### Symbolic links
Like a shortcut on windows, just a small text file containing the path to another file, like, almost literaly writing as a string. As such, not checked by the OS until they are evaluated Can be used on directories as well. 

declared `ls -s tieSymblinkTo nameOfSymLink` 

*Always interperted in the context they are in*, that is, creating  `ls -s a.txt lnkToA.txt` where `a.txt` is in the current directory, `lnkToA.txt` will not work after it is moved to a different directory. So, *hard link to a symbolic link will be interperted in the hard liks context!*

### Special Charecter Files
Most special files are in /dev,

### Path to file
A path to a file can either be *absolute* starts with `/` starts from home dir, or *relative* doesn't start with `/` and instad`pwd/PATH`.  
---lec3---

MAIN THEMES: 
    - indirection 
    - layering 
    - pattern matching 

## What is Indirection? 
Abillity to refrence something using name refrence or cotainer instead of value. Essentially gives us teh abillity to put things (operations, data) into black boxes which make them easier to conceptualize and work with. *Level of abstraction*

### Inode Numbers (a form of indirection) 
Instead of refrencing the files themself, or even their location, we refrence an index in a chart which will likely point to another chart which will point to the actual file mem adress. 

*orginize this, this is more UNIX spec*




----------





# 3 Little Languages: (bash) shell (not the programming guide, just overview opf philosophy), Regexp, filenames

## The Bash shell
A thin layer that enables users to talk to the OS. The shell is just used for tying things together, the real work is done outside of the shell by compiled (often c) programs. Withj the exception of a few commands that ussuly cannot be delegated since they are doing stuff to the shell iteslf ex: 
1. `.` run whatever commands are in a file. 
2. `cd` change the shell's own working directory. `cd` has to be implemented directly, *not it's command*
3. `exit 27` exit with status 27 

*little language philosophy* instead of one langugae to do it all, have specialized little languages for different purposes. Another ex is regexp. Perl is an attempt to have one language to rule them all. The shell is a little language to control OS. 

All computations used to be delagated outside the shell, which gave redicoulos overhead even to the simplest things. Now, it also has support for simple operations

### The Shell Envieroment
Includes:  
1. The current (initial) *working directory*: when you use a nonslash-starting directory. 
2. std input, output, error streams.
3. *environment variables*: Look like "USER=matan" "HOME=/something." 
4. User (from id), user id, group id, etc.

### Some random commands and whatsover

### Sed
with sed, you can pipe input into or include the filename at the end. string format is: 
`sed 's/t/T/g' text` where s refers to stream, t is the thing to change, T is what we will change it to, global means to it everywhere, not only for the first thing at every line. 



## Regular Expressions 
- `RS` concatenation by putting them next to each other
- `R*` zero or more ocurances of R concatenated
- `.` anything but newline
- `[abcd]` this is a set, either one of these 4 characters
- [a-z] accepts character ranges (what if u wanted to use [+-*]? [+*/-])
- [^a-zA-Z] negate a character set, all but those  character -- special charecter only at the start
- ^[^a\[\]]*$ -- anything not a and not square bracket
More recently, to deal with Unicode being huge [[:alpha:]]
- [][] match the open and close square brackets
- `^` line start
- `$` line end
- `\x` match x (whatever it is) precisely
- `grep -o` not for line 
- `?` after `*` tells it to match as few charecters as possible. Lazy vs greedy evaluation 


Don't get bogged down, there are like 10 different syntaxes! *See RegExp sheet at the bottom*

### Extended RegExp
`grep -E` (extended grep) 
- `R|S` match R or S -- this is the difference, it's very general this slows does and does backtracking 
- `(R)` group R -- just R? 
- `R+` is `RR*`

## Filenames
The specification of filenames can be though of as a little language following the practice of some finite automaton. 

1. First char: if `/` start at root directory, else, start at working directory. What if you start with nothing it all (empty string $\rightarrow{}$ invalid filename)
2. as you step through (perhaps recursively) you will always have a directory in mind
    edge cases for `/ /` $\rightarrow{}$ stay where you are.
    `..` parent directory. 
    else look for direc entry with that name, make it the "directory in mind." Remember that a directory is a partial func from names to files (file IDs, more precisely). If symbolink $\rightarrow{}$ interpret it.

In windows, exception to compilcation rules: *mount points* having multiple drives, and have c:/usr/bin d:/usr/bin. Instead we manage to have them all graph together.  
In UNIX we start with master tree on some drive, sometimes a certain directory makes you jump to the other tree, which is another file system on another drive, but to the user, looks like one big tree.





# Programming in the shell + Commands (all shell syntactical issues)

## Shell for configuraion
This is the real power of the shell, and what we talked about when we said "configuring the envieroment of programs" take a look at: 
 
```bash
(sort <foo.txt | sed 's/a/b/' | grep boot) > saveEx.txt
```
We configred the running of 3 diffrent programs, in a convinient chain, and saved the result to a file. Imagine doing this in cpp or even python! What a mess! Also, stronger than any configuration GUI. 

Note that commands also have deafults is not configured. 

### Exit status
0 means working, 1 ussaly measn werror

### Quoting (more problamatic than you would think!)
needed some way to have a space in a string (other than \SPC) (can't without quotes because bash thinks those are seperate args)

- Single quotes: explicit, what you see is what you get. A single quote may not occur between single quotes, even when preceded by a backslash. 
- Double quotes: Can place something in there that *gets evaluated* (suggested: use these only for a reason, use the simpler tool, not only for ptrformence, but for orginization -- think about your thinking time, preformence is negligable). Enclosing characters in double quotes (‘"’) preserves the literal value of all characters within the quotes, with the exception of ‘$’, ‘`’, ‘\’, and, when history expansion is enabled, ‘!’ ($ expands variables and ` expends shell commands, \ is likely to escape those). Example:
    
    ```bash
         "the exit status was $?" -- evaluetes $? and places value as string. Instead we can put there "What did you find: $(grep matan.txt helloWorld)"
    ```

## List of `sh` special charecters

1. "
2. '
3. \
4. `
5. |
6. \* -- *filename expension* -- globbing, not regExp. `*` is the `a*.c`
7. ? -- *filename expension* -- just like `*` but `?` matches one charecter only
8. [ -- *filename expension* -- a[b-z].c anything in the range -- matches 1
9. & Running in the background, example `emacs &` -- to terminate `fg` (foreground) and then `C-c`.
10. `=` assigments of strings `abc=def` `echo $abc` outputs `def`. 
11. SPACE 
12. NEWLINE
13. TAB 
14. < stdin 
15. \> -- std out
16. ; seperate args
17. ! -- not POSIX stdardised -- *negates the command* -- negates the exit status.
18. ~




### The building blocks: Special Tokens and words in bash
#### builtins
 |,&,|| (logical OR),&& (logical AND),() (run in subshell),<,>, <<,>>, `<<EOF` -- std in until you type EOF, ;; , ;
As an example: 
```bash
! grep lebron /etc/passwd && echo 'Lebron is missing'
```
*will echo lebron is missing if lebron is not found -- think about it*


#### words
nonspecial chars or strings jammed together with no whitespace. Makes for a single argument. Can concatanate string just by not putting a space between them.

#### reserved words
Are not for exceuting a program from from usr/bin. Instead they are: 
- if
- while
- that sorta thing that works with the shell's control structue
(see if, for, etc examples in scripting)

#### Execution blocks
execute command `{COMANDS}` as a block, i.e. inexsesible from outside, etc. `()` does subshell while `{}` executes as the parent shell. Think of to see the diffrence: 
```bash
(cd /etc; grep eggert passwd) VS {cd /etc; grep eggert passwd}
```
*the second option actually changes the envieroment --2nd a bit mroe efficient* 

To *run in parallal*:
```bash
(A & B) #or with curly braces
```

## Shell scripting
When writing, always have to enable execute premission on the file with `chmod +x scrpt.sh`
Some example scripts I'll need to orginize 

### Ex1: hello world with inputted name
```bash
#!/bin/bash
#you always must start with the following, btw, # is comment.
read -p "howdy what's your name?"
echo "hello " ${name} " nice to meet you!"
```

### Ex2 looping and printing special message for odds and evens
```bash
for $i in `seq 0 1 10`; do 
    if (($i % 2 == 0)) then 
        echo $i "is an EVEN number between 0 and 10"
    else 
        echo $i "is an ODD number bewtwen 0 and 10" 
done


```
### Ex3: args from CMD
```bash
#!/bin/bash
echo "Total arguments : $#"
echo "1st Argument = $1"
echo "2nd argument = $2"
```


### Ex3 create function 
### if then else (conditionals)
```bash
if COMMANDS
then COMMANDS
elif COMMANDS
then COMMANDS
else COMMANDS
fi
```

### pattern matching 
```bash
    case WORD in 
    (pattern) COMMANDS
    (pattern_2) COMMANDS
    (*) COMMANDS
    esac
```

### RandTopic1: Variable substuiton

- `${abc-def}` -- interpolate `abc` unless it does not exist then use `def`. 
- `${abc:-def}` -- treat the empty string as it was not assigned to. 
- `${abc?} exit because there is no resonable defult 
- `${abc?:}` -- add empty as unset
- `${abc=def}` -- if abc's value is empty assign abc to it and then interpolate def
- `${abc%.c}` printout abc but strip off a `.c` in the end if it has one.  

### randTopic2: Special Vars

1. $? most recent exit status
2. \$\$ current shell's process id

```
ps -ef | grep \$\$
matan    3548703 3548697  0 14:06 pts/18   00:00:00 -bash
matan    3556074 3548703  0 15:37 pts/18   00:00:00 ps -ef
matan    3556075 3548703  0 15:37 pts/18   00:00:00 grep --color=auto 3548703
```

3. `$!` last background process id.

### randTopic3: Field Splitting
```bash
x=$(pwd)
cd /etc
...doStuff...
cd $x
```
\$x will be only the first word and will not be usable to us. 

### randTopic4: Variable Substuiton 
- `${abc-def}` -- interpolate `abc` unless it does not exist then use `def`. 
- `${abc:-def}` -- treat the empty string as it was not assigned to. 
- `${abc?} exit because there is no resonable defult 
- `${abc?:}` -- add empty as unset
- `${abc=def}` -- if abc's value is empty assign abc to it and then interpolate def
- `${abc%.c}` printout abc but strip off a `.c` in the end if it has one.  

# UTF-8 
UTF 8 is a standard presented that allows for: 

- semi-random access: if you, at random, accsess a byte (it's rare that computers access a bit), you are immediately able to tell if you are the begining or middle (end by looking ahead) of the charecter 
- variable-width encoding: not all chars are the same width, which means that with some cost paid (by the prefixes on every byte) we are able to represent most commonuse charecters efficiently, and dip to  a larger representation only when absolutely necessery. 
- Backwards compatible with ASCII: an ASCII encoded document is validely UTF-8 encoded. 

## UTF-8 Representation
A UTF-8 charecter consists of 1-4 bytes. The first byte always looks like \[numBytesForCharInUnary_0_bitsForChar\], a trailing byte always looks like \[10_bitsForChar] a single caviet is that when we only need a single byte to represent a char (i.e, it is one of the 128 ASCII charecters) we don't put 1 for numBytesForCharInUnary, but instead put nothing, having [0_bitsForChar]. Note this is the regular ASCII encoding. When representing some char, mapping to a sequence of bits in binary, we first check teh length necessery and then lay it in this format, putting as many bits as possible in the first byte, as many as possible in the 2nd, etc. 

with 4 bytes there are $2^{3+6*3}=2097152$ rpresntable chars

# Emacs and Lisp 
Emacs is a UNIX based text editior charecterized for it's *extensibality*, that is, the user can easaly program new commands.  

Emacs consists of 
- *buffer* a location on the RAM used by emacs. Often, but not always (for example when running `M-x shell`) that location contains a file (we don't edit the file directly, change the buffer and save when suits us). All buffers can be saved to files. Per buffer emacs has the *point* pointer (cursor location `M-: (point)`) and *mark* (place you marked `C-@`).
- *windows* which are views of buffers. A buffer may have multiple or no views at all. 
- *frames* which are sets of windows. A frame is basically (at least UI-wise) a collection of windows.  


*Emacs Lisp* is a varient of the Lisp programming language. It is a scripting langauge specific to creating and customizing a text-editor (Emacs) rather than creating general programs.  


## The layers of Emacs
Emacs, like all other software is written in layers. Layers because: 
- easier to think about (can't think about mergeSort/React at the transistor level!)
- easy to divide up the work
- gives programmers on different levels their "spehere of comofrt" in which they know what they need to to get things done, and abstract the rest. 

## Emacs Commands
**coming soon**

### Emacs version control 
- .#F symlink to user editing the file, the user, the machine we are editing on, 
- F~ a copy of F before you have started editing it. 

## Lisp Syntax and Sample programs 
The regular command calling in lisp looks like (command-name arg1 arg2 ... argN) where the argumanets themself are often nested commands. Simple example may be 
```lisp
(goto-line (* (+ 2 5) 7))
``` 
**check if works**
takes us to line 49 = (2+5)*7

**coming soon**

### sProgram1: new split window 
Ussuly when calling `spilt-window-below` emacs opens a new window, but points it to the same buffer.
we may want to alter that behivor and have it, by defualt, point to a the next avalabile buffer (whichever one that is). Here's the program: 

```lisp
(defun my-split-window-func ()
    "this is a docstring for the function"
  (interactive)
  (split-window-below)
  (set-window-buffer (next-window) (other-buffer)))

(global-set-key (kbd "C-x 2") 'my-split-window-func)
```
breaking it down, note that all functions can be searched using `C-h f`.  

1. `(defun function-name () {code})` defines a new function (associates `code` with the function's name).
1.5. string at top is a docstring
2. `(interactive)` specifies that this a function that can be called directly by the user. Must be located top-level in funcBody. Without any arguments, the command will not accept arguments. Using `(interactive) "p"` specifies 'prompt for args', where "p\nPleaseInputX" will prompt "PleaseInputX" **check if this works**
3. the `set-window-buffer...` line does what we think, look how declarative!
4. **global-set-key** takes in KEY (which is a sequence of bytes uniquely identifying every keboard stroke, we can get it from the `kbd` method by specifyign it in plain text). To undo a key-set, `(global-set-key KEY 'self-insert-command)` binds the key back to self-insert-command, which simply appends it to the buffer at current position. 
5. eval with `C-x C-e` which evaluates everything withing the outmost paranthases. 


### sProgram2: middle-of-buffer
```language='lisp'
(defun function middle-of-buffer (&optional arg) 
  "Move point to the middle of the current buffer -- a function's docstring"
  (interactive "P")
  (goto-char (/  (+ (point-min) (point-max)) 2) 
```


### sProgram3: print of multiple args

```language=lisp
; simple to use output functions that displays all
;   its arguments in order,
; the outpLine version adds a newline at the end
; e.g.
;    (defvar x 3)
;    (outp  "3 times "  x  " is "  (* 3 x))
; prints "3 times 3 is 9"

(defun outp (&rest L)
   (dolist (x L) (format t "~A" x)))

(defun outpLine (&rest L)
   (dolist (x L) (format t "~A" x))
   (format t "~%"))

(defvar x 3)
(outpLine "3 times " x " is " (* 3 x))
```

## Different ways to evaluate lisp in emacs
*see the same title under compilation interpertation and in betweens*

# Compilation, Intrepertation, and In-betweens
What are our options when we would like to execute some source code? What are the advantages and disadvatages? 

### Motivation: Different ways to evaluate lisp in emacs
- in scratch buffer, evaluate using `C-j`, prints output in the next line. Under the hood, creates a tree containing all the commands. evaluates in postorder traversal (from the roots up to give the final return value)
- `C-x C-e` to evaluate and keep to mem(?) or print in buffer. 
- `M-x emacs-lisp-byte-compile` to compile to byte code.
**we are likely not going to be asked about these specifics and rather about the general themes, so for now, I'll pass**

## Different options when executing code
The following options are on a gradient:
1. Interpert data structures
    
    - easiest to implement
    - easy to debug because easy to "know where you are" on execution
    - slowest and perhaps largest option
    - used by elisp on reg exeuction
    - def machine independent, (possibly even virtual machine/iterperter independent)

2. Intrepert byte codes

    - byte codes are basically instructions for a virtual machine. That is, we have an add instruction, mult, etc, but instead of being interperted by the real machine, a program like the JVM interperts them. 
    - Essentially works with a huge switch statement (case per each byte code) and a stack teh program runs through. 
    - simpler to write and debug then 3, ussuly easier than 4 as well. 

3. Just-in-time compilation (JIT)

    - compiles from source or (more commonly) byte code to machine code on execution. Still machine independent
    - ussuly takes more time on boot-up, runs faster once it runs. 
    - difficult to implement
    - not the most efficient implementation since the compiler is "hurried"
    - typically continuously analyses the code being executed and identifies parts of the code where the speedup gained from compilation or recompilation would outweigh the overhead of compiling that code.
    - the compiled code out of it is not machien independent, but the input to it is. 

4. Compile to machine code

    - not machine independent
    - optimized as much as possible since compiler can its time. 

# Modes of Interaction and UI-UX Design 
When designing a UI, particularly for something as multi-functional as a text editior, we want a simple, easy, and global interface, but that comes at a cost of specialization. If we have an efficient UI tailored for any task, it's not one of the things above. Essentially, the conflict between expert-oriented and beginner-oriented. Think Photoshop (experts can do anything of anything, but tough to understand how to anything but the easiest stuff -- and even that is a comprimise) and old procreate with all its simplicity. 

## What's a mode
Specific envieroment that behaves differently. Good because specialization, bad because things that worked in another mode no longer do. Think `dired` in emacs.

# Python 
Why Python? Clean fast, readabale, passionate large community, saves prototyping time. 
## Brief History 
Spawned from a set of pedagogical languages in the Neatherlands. The idea is that most pedagogcial algos (hashs, sorts) are built in, enforces indentation. Succesfully was easy to read, tech, and write. 
## Python internals + Mutabillity 
Everything in Python has a type -- *an object*. Even integer/string literals, which in c++ just have a value, not an adress, in Python are full blown objects. 

Every object has: 
- identity (value in the hash table of the entire namespace) -immutable
- type -immutable
- value -mutableOrImmutable
- attributes, extra values associated, mutableDependingOnType
- methods, functiosn taht work on the object 

### Equality

Considering that, *Equality* can be checked: 
```python 
a is b #compares identity, faster

a == b #compares value of two objects
```
### Variables
variables are just pointers (labels) to objects, nto the objects themself. we can change what they point to (as to something of a completly different type, but we haven't mutated the object, just changed the assigment of the variable).


### Assign by ref vs val
When doing assignment, Python *always automatically assigns a refrence*. That means, unless some special behivor is defined (in cusotm class or something),
```python 
a = b #assign b to a where b is some existing object of some type
a is b #returns true, always
```
Now, here's the caviet. If a and b have a mutable type, or we assign something to 
```python 
a = b #assign b to a where b is some existing object of some type
a is b #returns true, always
a[2] = "helloWorld!" 
#a is b is still true and a changed because lists are mutable
```

Yet, if the have an immutable type 
```python 
a = b #assign b to a where b is some existing object of some type
a is b #returns true, always
a = 3 
#a is b is FALSE and changed because int's value never changes, we must assign a to a new object. 
```
Even if a,b were mutable and we assign to `a` a whole new object (`{'matan':'cool',1:2}`) then a is b would be FALSE. 

## Python types
A non-exhustive list: 
- Numbers
    - int 
    - float
    - complex 
    - bool
- Sequences
    - str
    - list 
    - tuple --prefered, faster and not mutable
- Mapping
    - dict
- Callables
    - functions
    - methods
    - classes (like the template, not the class object)
### Zooming-in; Sequences
Assume `s` is a sequence. Here are some operations: 
- `s[i]` index into s, reg for `0` $\leq{}$ `i` $\leq{}$ `len(s)`. for othe rway `s[-1]` indexes from the end (end-whaterv)
- `len(s)`
- `s[i:j:inc]` start at i, end at j (non-inclusively), at skips of `inc`. can leave out, `i`, `j` or `inc` with reasonable defults.
- `min(s)` 
- `max(s)`
- `list(s)` returns a list with the same element,if `s` is some other sequence, will convert to list. Can do same with tuple, whatever. 

`s[i:j] = a` a is a sequence. Set those indexes to a single index a.

Can contain different types. 

#### Further Zoom; Lists

`list.append(a)` add list or single elements tot the end. when appending a list we append it's elements, not the list. -- fast by allocating extra space, waiting for the next append. Allocates a whole list and moves everything when you append of the end. occusionally $\mathbb{O}(n)$.  

```python
#some common operations: 
s.count(v)
s.extend(a) #--push, 
s.insert(i, v)
s.index()
s.pop(i)
s.pop()
s.sort()
s.reverse()
```

### Zoom-In; Dictionaries
```python
d[k] = *whatever* #where k is any immutable object can be used to index in. Immutable because. Err if d[k] does not exist when invoked
d.keys()
d.values()
d.has_key()
d.get(k, defeult) #if value is absent, you can specify what is retrieved. 
len(d)
d.clear()
d.popitem() #grabs a (some) key value pair and removes it. 
d.update(d1) #updates d with dict d1, d1 wins on conflict. 
del d[k]
```
Has a domain and a range (partial function). Domain is the set of all keys you have assigned through. *aAgain, distinction between having the empty value or being unassigned.*

*implemented:* Using a hash table, where every `k` is hashed to some object. My thought is "just take the value!" but the problem is that we need the object to maintain a ref but with immutables it will remain yah. 

### Callables
```python 
f = lambda x: x+1 #a lambda expression is a nameless immutables. Useful when you don;t wanna give it a name, useful when you wanna pass in a simple function, for example 
def f(x):
    return x+1

#======================================
def f(x, *args):#rest is a list which contains all the leftwover args 
    return [x, args]

def f(x, **kwargs): 
    return "whatver dict has been specified"
#even when kwargs is not specify, use the name (x=1, y=2) in the call where those are the parameters called. CAN USE BOTH KWARGS AND ARGS in one function 
```
### Namespaces
Is a dictionary where keys are Python identifiers. Values are functions, methods, etc. Our program is literally running on a dictionary. 

There is a namespace associated with each class. It has a member which contains its name as a dictionary.  try `c.__dict__`

Names starting with `__` are spacial. 

### Modules & Import
give you control over your namespace. A primitive example is a sourcefile, which assigns values (objects) to name (indexes of our "main" dictionary).

Import creates a new namespace, reads & executes foo.py code in it, assign that namespace to foo. So foo is itself an index in the dictionary. You can use that new namespace after. such that import file. print(file.pi) will get the elements from that new namespace



# Networks, Internet spec basis 
Running programs across multiple, rather than 1 computers. 

## Different models of coordination

### Client-Server
Server is in charge of maintaining the state, single authority, allows to coordinate, server always knows the current state of the system. Earliest Form.

### Peer-to-Peer
Distribute the data amongst the peers, more complex. Maybe everyone is responsible for a certain type? 

### Primary-Secondery computing 
Primary assigns to all the seconderies work, so that data compunicates back up to primary. 




## New issues for networks

1. **Preformence**: Overhead for shipping data. Matters in production, on large demanding apps. Two aspects to preformence:

    1. *Throughput*: how much work can your application do per second? (operation per second, def operation depends on your app). Generally meassures server-side. Of course, you'll want to maximize. 
    How to improve throughput? add more nodes, things improve in parallal.
    2. *Latency*: Time difference between request and response. How to improve? cheat and cache data, i.e. avoid the actual request.

    To some extent, these are competing objectives. Throughput increase for example, to wait a bunch of request and process them all together. 

2. **Correctness**: supposed we do *out-of-order execution* to increase latency (or throughput?) Here's an example:
    
        imagine $c_1$ requests before $c_2$, but $c_2$ gets faster to the $s$. What if $c_2$ request changes the response for $c_1$'s?

    we use *serialization* to solve, where serialization is **I am not sure** something like explaining system action based on the order... Maybe you are trying to do something based on chache that is no longer accurate (trying to buy a ticket that is cached as avalabile, but no longer is). Most chaches can be somewhat out of order (Google maps, for example, not money or anything important).  

    Problem of *cache validation* some chache on the server, wanna know how correct it is, just asking server is not efficient. 

3. **Security** The hardest of the 3. Skip for now.

## History of Networks
Brief overview of CS 118.

### Circuit Switching 
Phone in LA wants to call NY. The whole mux-demux operation, but with extra steps (middle stations along the way). You have to relay across many many stations. While the call is going on, nobody can use those wires. *A lot of ouppurtunity for sharing*.

### Packet Switching 
You don't reserve wire exclusively. Take $m$ and divide it into $n$ 100Kb packets. Dump packets into network, s.t packets are somehow gonna get to NY (maybe some part of $m$ specifies that), they "eventually" arrive at NY, perhaps not in the order we sent them, perhaps some got "lost."

Each packet is treated independently, the route of each packet is done differently. *It's the recipent's job to make sense of the jumble of packets they get* (deal with duplication, order chance, and loss of packages). A traditional phone is not enough! You need more intelligence. 

Paul Baran proposed (1962), built (1969) here at UCLA. 

Problem of Buecracy: many manufacturers, didn't want to bow down to anyone else's standards. Architectures very different. Need a peace treaty to allow this network to exist and devices to talk to one another. Called the...

## Internet Protocol Suite + Layers of Internet (again, the abstraction)
Protocol, coming from deplomacy (trying not to offend one of the other companies). Specified in the Internet *Request For Commments (RFC)* -- didn't want to offend anyone (here's an idea send me comments for improvement). Do it this way if you want to work with the rest of the world. The constituition of the internet. 

Basic idea: *When possible, we try to build these protocols out of layers (of abstraction)*, we can't think precisely of "what bit." Levels (low to high):

1. *Link layer* talks about what happens between 2 nodes. hardware oriented
2. *Internet layer* talks about individual packets, that could be sent from anywhere anywhere else
3. *transport layer* Having data channels. Abstract the mess of packets. new york is reacieving LA is sending 
4. *application layer* We take the (channels and packets and tailor it for an application) application layer for web-browsers


### Protocols: networks: APIs: Program 
*API* is a contract betwen a user and an implementation. higher leverl, just what u give what u get. You don't need to, and often don't get to, know the underlyings. 

### Zoom in: Internet Protocol (at the internet level)
IPv4 and IPv6 We focus on 4. Everything connected to internet, we have a 32 bit integer written in 8-bit number. Supposudly unique. (RFC 791 -- 1981 -- Jon Postel)

A packet contains the header and contents, where the header has sender's IP add, recipients IPaddr. Protocol number -- to support highr level protocols. TTL -- time to live (?), hop count, too many places, packet dropped -- Prevent infinite loops areound. Has a checksumn (16-bit) --  for data integrity -- catch transmittion erros. Parity of some packet area.

RFC 8200 (2017) 128-bit IP address (less efficient, but we need new adresses!) + other stuff. Current internet is a hybrid. 


### Zoom in: Transport layer
UDP (User Datagram Protocol) RFC 768. Thin layer over IP. For application that want to send packets and nothing else. (closer to 2 than 3). 

TCP (Transmition control protocol) RFC 793 (Vint Cerf, Bob Kohn) For application that want more than that. We want to have data channels, we want to know it gets there. 
- Provides stream of data which is realiable (ship out data, the other guy gets it, but in realit ythere is re-asking for lost data, but user doesn;t know or care) 
- Ordered
- Error-checked (has it's own checksum) *fundemental property each layer doesn't trust the next layer down when it comes to data integrity* -- too many screwups. 

Has to: 
- divide into packages
- reasembley reasemble packets in correct order
- retransmition if package lost -- resend. 
- *hardest part:* transmission control. *flow control* sender may have a lot of packets to send, but it shouldn't send them all at once becuase router would drop them. *collective property rather than individual property*. TCP didn;t actully get it right. 

Many protocls are built atop TCP/IP -- mostly commonly used transport layer protocls. Examples: 
- RTP -- real time transport protocol. Stripped TCP designed for video stream. Varient for TCP. Specified RFC 3550 (2003). Reasonable real-time preformence, willing to give up realiability, some data may be lost, we are okay with that (some pixels missing, but rather keep watching). TCP would cause gitter due to the re-transmission. (ZOOM is a varinet).
- HTTP more later

## The world wide web (WWW)
Tim Berners-Lee (CERN, 1991). Came up with a protocol, HTTP, and HTML (data format). 

### HTTP (1.0)
Very simple protocol request/response

    `GET index.html HTTP1`

Header at the HTTP layer, and body. iterations of additional features HTTP 3 is upcoming. HTML has evolved. Where the pressure come from? What is the reason for these changes? What to use to be bleeding edge. 

HTTP/1.1 (RFCs 7230-7235)

Nowdays, HTTPS is more popular, it encrypts the data from client to server and vice versa. Diffie-Helman!

In HTTP/2: 

- transport efficiency 
- server push (server can message u don;t have to check all the time)
- pipelining send 3 request at once (one header), all in a batch, speed. Disatvantage -- wrong order. 
- Multiplexing: Independence of tabs with 1 HTTP connection to the same server. 

QUIC bypassing the TLS TCP layers (stil IP ). QUIC version1 just replaces them but isn't HTTP. HTTP 3 ontop of QUIC v1 (RFCs 9000-9002 MAY 2021). 

### HTML 
HyperTextMetaLanguage, took from SGML (standard generalized markup language). cross publisher text

## JS
as the dynamic language

# Questions
1. How to permenantly change a keybinding in emacs.
2. what's the deal with all the parantheses in bash? 
3. how do I deal with the 
```bash
x=$(pwd)
cd /etc
...doStuff...
cd $x
```
field spilliting?