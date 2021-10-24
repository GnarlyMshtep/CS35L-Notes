Today: Layers, Pattern Matching

## Layers, In general, (but more specifically in Emacs)
Software is generally built in layers. For example, emacs is written (bottom to top): 

- machine x86-x64
- Linux kernel -- interacted with emacs when syscalls are made for OS to, for example, display stuff on screen. (1.5ish layer)
- emacs executable, dozens of libraries
- window.c (c/c++ level). 
- emacs lisp (80-90%, about, don't quote him) Examples: `subr.elc, dired.elc, calender.el`
- (1/2 layer) your personal init file
- ELPA: Emacs community code, largest, exists for customizable devs and stuff.  

When starting a software project, designing it by levels, such as this, will give you a good idea of what's going on. 


## Why Layers
Definitely need to modularize so you can divide the work and though... ?

Answer: **Levels of abstraction!** Allows you to give different developers to know enough to get their work done. Also, we think in levels of abstraction naturally (somewhat philosophical 😊). Impossible (or at least very difficult) to think of simple algorithms such as merge sort at the logical gate level, we need black boxes to to build on.

## The shell (seen in layers)
Another example: *POSIX Shell*. Shell refers to being on the exterior of UNIX, wrapping around it so that users can talk to it. Thin as possible barrier between you and the system. Give users control and let them get close to the core. 

When you write a shell command, the shell program finds the appropriate function and executes it, returns output, and then wait. *The real work is all not done by the shell, the shell just navigates you to the correct place*

### Aside on Grep (used as an example in class)
1. `ed`:  ________________________________
    globally look for regular ... `g/re/p` globally look for this regular expression. With grep rather than `ed`, doesn't need to have all data in memory. `grep` is a big deal, a lotta people think about optimizing it and stuff.
2. `grep` read a line, see if it matches, print it if does, else do not, then, go on to the next line. See *regular expressions*

## Regular Expressions
*REs are a smal language for (simple) pattern matching*, the *shell* for example is a *little language for control of OS*

Uses ordinary characters to stand for themselves (all [:alpha:] and [:numeric:]) 

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


Don't get bogged down, there are like 10 different syntaxes! *See RegExp sheet at the bottom*

### Extended RegExp
`grep -E` (extended grep) 
- `R|S` match R or S -- this is the difference, it's very general this slows does and does backtracking 
- `(R)` group R -- just R? 
- `R+` is `RR*`

## "Little Language" Philosophy
Recap: *REs are a smal language for (simple) pattern matching*, the *shell* for example is a *little language for control of OS* 
Instead of one for all, many specific small task specific(ish) 

`sed` for 1 by line editing (stream editor)
Competing text editor `awk`  -- slightly bigger 

This idea is *contreversial* -- it's too much to teach people how to do this! There has been attempts to have one language that does all the stuff, example, (Larry Wall) Perl.

### Coming back to the (talking about) the shell
A language for controlling how to run programs. You don't want a lot of work in the shell, it's slow. The shell is a tool for setting up and environment that a program to run. 

What is this "environment" thing? Well, it includes: 
1. The current (initial) *working directory*: when you use a nonslash-starting directory. 
2. std input, output, error streams.
3. *environment variables*: Look like "USER=matan" "HOME=/something." 
4. User (from id), user id, group id, etc. 

Shell built in commands -- cannot be delegated. Examples (these can't be delagated since they are doing stuff to shell): 
1. `.` run whatever commands are in a file. 
2. `cd` change the shell's own working directory. `cd` has to be implemented directly, *not it's command*
3. `exit 27` exit with status 27 

Most commands from the shell are farmed out to separate executables. 


### The little language of filenames
Small control program for walking through the file system. Execute the commands in order left to right. 

1. First char: if `/` start at root directory, else, start at working directory. What if you start with nothing it all (empty string $\rightarrow{}$ invalid filename)
2. as you step through (perhaps recursively) you will always have a directory in mind
    edge cases for `/ /` $\rightarrow{}$ stay where you are.
    `..` parent directory. 
    else look for direc entry with that name, make it the "directory in mind." Remember that a directory is a partial func from names to files (file IDs, more precisely). If symbolink $\rightarrow{}$ interpret it.
        Over the years, people wanted to (over)complicate it, and make it a larger little language. 

        Exception to compilcation rules: *mount points* having multiple drives, and have c:/usr/bin d:/usr/bin. Instead we manage to have them all graph together. 
        You start with master tree, where a certain directory makes you jump to the other tree, which is another file system. -- looks like one big tree. 


## Random Stuff (mostly instructions)
1. `(command ; command ; command)` self contained stuff, not effecting your actual environment and stuff. 
2.  `C-x C-e)`: evaluate a lisp expression, to eval and create funcs in buffer, for example 
3.
4.
5.


## Questions
1.
2.
3.
4.
5.


# RegEx cheat sheet

Regular Expression FUll Cheatsheet (For quick look) :)
note: for downloading visit https://buggyprogramm\ner.com/regular-expression-cheatsheet/
------x-------------------x----------------x------------x----------------x-------------

# -------------- Anchors --------------
^   →  Start of string, or start of line in multiline pattern
\A  →  Start of string
$   →  End of string, or end of line in multi-line pattern
\Z  →  End of string
\b  →  Word boundary
\B  →  Not word boundary
\<  →  Start of word
\>  →  End of word


# ---------- Character Classes --------
\c  →  Control character
\s  →  White space
\S  →  Not white space
\d  →  Digit
\D  →  Not digit
\w  →  Word
\W  →  Not word
\x  →  Hexadecimal digit
\O  →  Octal digit


# --------- Quantifiers -----------------
*     →    0 or more 
{3}   →    Exactly 3
+     →    1 or more 
{3,}  →    3 or more
?     →    0 or 1 
{3,5} →    3, 4 or 5
Add a ? to a quantifier to make it ungreedy.


# ------- Special Characters -------------
\n   →  New line
\r   →  Carriage return
\t   →  Tab
\v   →  Vertical tab
\f   →  Form feed
\xxx →  Octal character xxx
\xhh →  Hex character hh


# --------- Groups and Ranges -------------
.       →  Any character except new line (\n)
(a|b)   →  a or b
(...)   →  Group
(?:...) →  Passive (non-capturing) group
[abc]   →  Range (a or b or c)
[^abc]  →  Not (a or b or c)
[a-q]   →  Lower case letter from a to q
[A-Q]   →  Upper case letter from A to Q
[0-7]   →  Digit from 0 to 7
\x      →  Group/subpattern number "x"
Ranges are inclusive.


# ----------- Assertions ---------------
?=         →  Lookahead assertion
?!         →  Negative lookahead
?<=        →  Lookbehind assertion
?!= or ?<! →  Negative lookbehind
?>         →  Once-only Subexpression
?()        →  Condition [if then]
?()|       →  Condition [if then else]
?#         →  Comment


# ------ Pattern Modifiers --------
g   →  Global match
i*  →  Case-insensitive
m*  →  Multiple lines
s*  →  Treat string as single line
x*  →  Allow comments and whitespace in pattern
e*  →  Evaluate replacement
U*  →  Ungreedy pattern
*   →  PCRE modifier


# ------ String Replacement ------
$n  →  nth non-passive group
$2  →  "xyz" in /^(abc(xyz))$/
$1  →  "xyz" in /^(?:abc)(xyz)$/
$`  →  Before matched string
$'  →  After matched string
$+  →  Last matched string
$&  →  Entire matched string
Some regex implementations use \ instead of $


# ---------- Escape Sequences ------------
\ Escape following character
\Q Begin literal sequence
\E End literal sequence

"Escaping" is a way of treating characters which have a special meaning in regular
expressions literally, rather than as special characters.


# --------- Common Metacharacters ---------
^ 
[
.
$
{
*
(
\
+
)
|
<
>
The escape character is usually \


# ------------ POSIX ----------------
[:upper:]  →  Upper case letters
[:lower:]  →  Lower case letters
[:alpha:]  →  All letters
[:alnum:]  →  Digits and letters
[:digit:]  →  Digits
[:xdigit:] →  Hexadecimal digits
[:punct:]  →  Punctuation
[:blank:]  →  Space and tab
[:space:]  →  Blank characters
[:cntrl:]  →  Control characters
[:graph:]  →  Printed characters
[:print:]  →  Printed characters and spaces
[:word:]   →  Digits, letters and underscore