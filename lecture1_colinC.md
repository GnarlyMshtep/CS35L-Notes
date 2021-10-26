# Lecture 1

### Patterns of Software Design
- Scripting
  - Want to code something up quickly just so it works
- Dependencies
  - Sometimes we need something done before something else can be done
- Version Control
  - Need to track of how our software is evolving (branching, merging, ect)
- Self Reference
  - When we need to make a change to git, we use git
- Debugging
  - We spend a sizeable about of time debugging
- Client-Seriver
  - Need clients to interact with our services


Bring printout of work on assignments to exams since questions often reference stuff we do on assignments.

### Linux Servers
Use the following Linux servers
- lnxsrv11.seas.ucla.edu
  - Also 12, 13, 15

Put the following in our `$PATH`
```
PATH=/usr/local/cs/bin:$PATH
```
- we'll instead run the newer executables in `/usr/local/cs/bin`

### What We're Doing
- Programming
- Data Structure Design
- Integration
  - Making different pieces of software work together
- Configuration
  - Making programs run a specific way (eg run on port 8080)
  - Setting up an environment for the rest of our programs to run
- Testing
  - We want to test our code well
- Security
  - We can't take this for granted
    - 2 ways of doing
      - security by obscurity: write it in such a weird way that an attacker won't be able to understand it
      - security as an addon: we have an insecure system, but we install plugins or such to make it secure
  - If we want to make a system secure, security must be built into our system.
- Forensics
  - When our security fails, we need to figure out what to do
    - logs, log analysis tools, ect

### Emacs, POSIX Shell, and POSIX File System Organization
- What problems do these three systems address?
  - Functionality
  - Reliability
    - what happens when our power goes out, what happens when our program crashes
    - We care about persistence: can we restore state
  - Performance
    - Reliability and Performance are competing issues, we have to give up some of one to get more of the other
  - Understandability
    - Can we look at how they're implemented and understand they work
  
In the shell, we can use control flow
```
true || echo ouch
>>
true && echo ouch
>> ouch
false && echo ouch
>>
```

```bash
# ps: all processes on system and 
ps -ef > psout
```
- run ps and pipe output to file

```bash
# Read files, and print contents of files to stdout
cat /etc/passwd /etc/os-release
```
- in emacs, to read manpage, run `m-x man`

- Basically every shell command has a `--help` flag
  - Brief manpage
  - Also `info` command gives more detailed output than manpage
  - eg `info cat`
- Piping ` A | B` is fast since `A` and `B` can run in parallel
- To feed stdin to cat
- `cat /etc/os-release - /dev/null </etc/os/passwd`
  - Similar to `echo abc | cat /dev/null - `
    - Takes input from stdin
- Redirect stderr
  - `cat dog 2> caterr`
    - The flag `2>` means to redirect stderr
  - `cat` alone reads from stdin

File System
- `/dev/null` is a special file that contains no files and doesn't let anything be written to it
- `/dev/full` is another special file that whenever we write to it, it tells us that we've run out of space on our device


## Discussion

### Linux - File system and Processes
- Everything in linux is either a process or a file
- The file system in Linux is a tree structured heirarchy
  - Absolute paths are given from the `/home/` directory, whereas relative paths can be from the current directory
  - Whenever we view a file, we're actually viewing the soft link to the file, which basically serves as a pointer to the contents of the file
```bash
# make a hard link to a file
ln a.txt HL
# make a soft link to a file
ln -s a.txt SL
```

```bash
soft-link -> a.txt -> inode
# inore stores the contents of the file
hard-link -> inode
```

#### User Permissions
```bash
# ls  binary
---   000
--x   001
-w-   010
-wx   011
r--   100
r-x   101
rw-   110
rwx   111
```
Example
```bash
chmod 755 a.txt
# first number is user
# second number is group
# third number is everyone else
```

#### Shell Commands
```bash
wc a.txt # word count
^bar^foo # rerun last command and replace bar with string foo
>> # append output to file
> # rewrite file with outputs
```

