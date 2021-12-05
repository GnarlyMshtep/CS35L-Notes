
# Git 

### What does git do?
- keep track of your projects history (that is, all your previous commits)
- keep track of your projects future (that is, the staging area and the work you are currently doing and will be commited). Eggert claims this is equally important. 

### The git workflow + fomat commits
1. Edit your file -- changing working files, git doesn;t know or care about them, you don't like? always refert with `git checkout -f` throws away what is now, esser version of reset.
2. `git add FILE1 FILE2` files you want to commit in the following commits. Puts the contents of these files in your future (*staging area*), can keep editing.
3. `git commit` put into the repo as fresh commit, You will be prompted for commit message. 

Suggested format for commit message: Single line summery of the change, 50 chars is nice. next line empty. Further explanation motivation. In large projects, commits are marketing tools meant to convince other users to pick up the change.  

## What are some git commands? 

### From lesson 1, general workflow
1. `git init` create a new empty git repository. Don't use a lot, because you do this when you started a completely new project (it's more often to work on project at work).
2. Examine the status of the src code. `git log` 
    
    1. meta inforr. about 
    2. **a ton of options**, example `--pretty=fuller 
    3. has commiter and author diffrence, to commit is to actually add to the repo. commiter is the reviewer. Allows you to backtrace problems, responsibility and change tracker. Not all CS stuff, but buisness and social engineering, keep track of human motivation. 
        
        - `git log A..b` log everything between commit A to B. Can just type a prefix of A and B (as long as unabmbigous). This is important property of git, mention only first 5 or 6 digits, because it knows u don't need the whole shabang.
        - `HEAD` -- most recent commit 
        - v3.8 (tag name, branch name)
        - `c^` the parent of commit `c`. Recursive `c^^^` also works
        - `c^3` repeat `^`*3 back. 
        - `git diff c^^` == `git diff c c^^`, also works for git log. 
        - `git show nameOfCommitOrId`
        - `git show nameOfCommitOrId: FILE`
        - `git blame file` -- list of lines in file and showing who put that line into that file, and when -- find who is to blame. To find out "why was this line put in, grab commit id and ask for a log using git log""
        - `git blame COMMIT -- FILE` (exploring a cave, see why the code is the way it is).

3. `git clone` copies an existing repository example

        `git clone https://git.savannah.gnu.org/git/diffutils.git`

    When we clone we have a `.git` folder, which contains the entire history of the project. The rest is the src code. 

    1. look at src code
    2. change the src code!

4. `git diff` -- look at what you changed. Usally done at IDE level. Look at the status of the dev tree. 
5. `git status` for above, you see what you changed, what repo you are on, etc.  
6. `git ls-files` which files does the repository control (current). can be used as part of `grep Error $(git ls-files)` to put is as part of a commands and look for all error for example in all src control files. *Really fast!* 
- `grep git ls-files` is so common that git has `git grep REGX_PATTERN`                          
7. `git add FILE` or `git add -A`


## Branches, merging, patches
As we discuss all these concepts history-formatting concept, keep in mind *the philosophical tension between keeping a clean and beutifull history vs keeping a realistic history*. A realistic history may bring to a better undertanding, but looks kinda bad, is messy, ugly, hard to see main pints. Buetifull history may not relect what really happen, which is issue when your trying to figure out why things are the way they are right now. 

Eggert's advice is to not be too extreme either way.


### What is a branch 
- A branch a lightweight automatically-moveable pointer to a commit, allowing you to keep track of the commits you care for most. 
- The branch is identifying in a nice way where you would like to be in the tree ussuly in a leaf in a tree, a line of development
- lightweight in the form that all all the commits are in the tree, the branch is just a pointer to a spot in the tree, which can be found at `.git/refs/heads. In the heads folder, the heads are just (nested) files which contain only the commit id. They are dirt-cheap! make as many as you'd like!
- when you are on a branch, git remembers you are on branch, and when you commit, it adds the new commit to the tree and moves the branch pointer to point to the new commit.
- The *detached HEAD* state means that your branch has no name, which you likely don't want to. If you commit while detached (which is not reccomended), you will grow the tree, it commits to where you are and it would commit regularly, but you would only be able to return to that location by remembering the commit id and `git checkout`ing it later (think about it, how else will you (esaly) find a commit somewhere in the tree that isn't the ancestor of any branch heads?).
### What motivates people to branch? 
1. maintain old releases
2. Conservative AND radical options for users and developers to work with
3. Different visons for what the software should do
4. Portabillity 
5. new features (collide at first, or maybe not everyone would want)
6. parallal work 
7. refactoring (not changing any of the features, but reorginizing the code)
8. Competition (not know what working on). 


Mainline branch, all the new changes from the devs/ Not well testeed just yet. Users shouldn't use. Will eventually turn into the next release\
```
    -\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\* (mainline)
        -\*-\*-\*- (release 20 -- slow and conservative)
                -*-*-*-* (release 21, eventually and cautious)
```
- motivation is to maintain all releases (oh man I did not pay attention he might have said more things)
- satisfy developers who want to be conservative and radical 
- also satisfy users at different levels of radicality (use mainline vs an old reease) 
*another motivation: competing ideas for how software should run*
*another motivation: compatebility with different systems* 


### Commands for branching, merging, and working with upstream
- `git branch` (list all local branhes)
- `git branch -r` (list all remote branches)
- `git branch --track emacs-27 origin/emacs-27` (I want to get a copy of the emacs 27 branch locally, if someone makes a change, I want to see it locally (emacs 27 is the name of our local))
- `ls -lt` $(find x -type f) (sorry, wasn;t paying attention)
- `git checkout [branchname] OR [commitid]` (change the local files to match a local or remote reposiroty)
- `git checkout HEAD` (last commit on current branch)
- `git checkout -b my_new_branch` (create new branch and point to it)
- `git branch -d myNewBranch` (delete the branch -- gets rid of the lightweight pointer, the commit still exists and is accesable by id).
- `git branch -m oldname newname` (rename a branch-- good for typos)


- git fetch (make my local copy of the upstrem repository, match, what's upstrem -- make your origin/ branches overwrite what is)
- git merge  (merges remote changes into local copy -- Eggert doesn't like -- what if your local changes disagree? Makes mistakes too often (according to Eggert))
- git push (the oppisate of a fetch, push your changes up to the remote)-- patch files are number 0001, 0002, ...
- git am 0* (pathc pathc files to upstream -- doesn't merge or create new mranches, just adds to your branches, the branch gets longer.)
- git push (the oppisate of a fetch, push your changes up to the remote)-- patch files are number 0001, 0002, ...
- git send-email (send a collection of patches as email)


#### git patch workflow + commands
- `git clone local local2` (get 2 compies of the repositories locally, where one mirror what's upstream (remote))
    - work in local2, and once your'e happy (do work, meaning git add and commit)
    - get a listing of the work using `-git format-patch <since>` (this outputs a patch (text) files that contain all of the commits you made up until the main branch head (or commitid))
- `git fomat-patch main mybranch` (all the changes from main to mybranch, another option)
    - what can you do with those patch files? You can email them to your mates, ask them if it looks good, ask for advice without trashing the history (see philosophy of history maintence). 
    - Now, after the approval of your mates, `cd ../local`; `git apply name-of-file.patch` or use `git am *.patch` to apply all patch files

### Merging 
The big gurilla of git. Without merging, we would just have a ton of branhces and get lost. Users just want to see one stable current best version. Combine all the best features into a single commit of several branches.

Can merge many branches at once, but merging 2 at a time is safer, easier, and less error-prone. 

- suggestion: check your merges for messups\
    - git diff A (first branch we merged) M (merged branched)
    - git diff B M 
    to know if these are right, before the merge, git diff A, git diff B. 
- git is dumb, knows about text lines, not what you are doing. 

### How does a git merge work?
In the following steps!

1. find the closest common ancestor
2. apply both changes of merges. 
    - if one brnach changes fileX the other branch changes fileY -- easy merge! 
    - What do you do if they affect the same file? If the affect different *areas* (git developers use that word very carefully) of the file -- still workss, in general. *just a hueritic, not always right, sometime misses collisons* 
    - if the same area is affected (and it is recognized), we have a problem. We resolve as follows:
        - git will tell you "watch out for files f and g" -- there was a colision there it couldn't handle
        - conflicts are presented like this: 
        ```
        Here are lines that are either unchanged from the common
        ancestor, or cleanly resolved because only one side changed.
        <<<<<<< yours:sample.txt
        Conflict resolution is hard;
        let's go shopping.
        =======
        Git makes conflict resolution easy.
        >>>>>>> theirs:sample.txt
        And here is another line that is cleanly resolved or unmodified.
        ```
        - where your side is ussuly presented first, seperated by `=======` and then their side. 
*diffrence in pushing* -- you add your files upstream. 

### Alternative to merging (merging vs rebasing)
Rebasing -- want to merge but without the hustle. 

    -*-*-*-*-*-*-\ --- - -- - (main)
                  \-*-*-*--* my branch (HEAD points to last commit here)

Started new branch but people kept working upstream. Could merge myBranch and main, but also can `git rebase main`  
- appplies all the delats from our branch to the leaf of the main branch. Works only for sufficinetly small branches or it may be too much of a mess. 
- *plays into the conflict between buetifull and realistic history*. 

## Misc 

### .gitignore
In the main repository there's a file called .gitignore telling git which files should it not track. There's also a gitignore in the .git/ folder, which allows you to ignore things just for you, not for the project (since that gitignore file is not tracked). 

#### The .gitignore little language
Set of patterns telling git which files should be ignored. For example: 
    
    *.o -- ignore .o files.
    a.out 
    /node-modules

simillar to shell pattern matching, but not exactly. For example 
- `*` globbing pattern, shell style 
- if start with `/`, anchor to the current working directory. 
- *.o matches all something.o in any folder strting at the current working directory
- `!abc.o` don't ignore this, priority to whatever is first. 
- everything relative to where the .gitignore file is at. 

#### What and why to ignore? 
Opinions are split!

Some of our software components should not be in the git repository. That is, because they are 
- secret 
- utilities that we are not changeing, 
- compilations of the src. (or anything generatable from something else), perhaps unless they are small and take a lot of time to regeneate 
- Should avoifing putting machine dependent things on repository  

The idea (in general) is to only have src you are working on, and be minimalistic. For example, `.o` should not be included. 






### .gitconfig
we have global and local git config files that add variables and all sort of requirements. 

### Hooks
Hooks are certain scripts that git can run when certain actions take place. In `.git/hooks/` one can find all sorts of sample scripts. They are marked `something.sample`, where `.sample` tells git not to actually run the script. They can add all sorts of custom behivors for your script.   


# C 
C is the lowest level software tools (programming language) that runs everywhere. Created by Ken Thompson and Dennis ritchie in the mid 70's at Bell labs. 

### What is C used for today? 
- rarely used for end user applications, C++ (and higher level friends -- python, etc) are more common for end users. and for that reason, it's usage is declining.
    - although, it's market share is declining that may be just because many more end user applications are being written -- not necesserily less C applciations.
- When C is used, it's primarily for components/auxilary tools for software developments or embedded devices. 
- when your application must be fast and efficient, you go to C, but you also pay a cost in development time (C is primitive and u do everything) and security (due to low level mem stuff) that's why people go down to it only when they have to. 
- One example is Python, another is the Linux Kernel (Big C program that run under all ), Emacs,... 
    - when something is used very often we care about it enough to invest the time to write it in C, overcoming slow dev time and carefullly checking out errors. 


C++ was originally a C compiler. C++ implements all those features using low level tricks on C. 

##  Compilation in C (some GCC, but the focus is on C iteslf - see full `#` gcc section)


### Steps of compilation
The phases which C/C++ compilers go through to generate C programs

Programs are in levels: (bottom to top)
- machine (we don't talk about much)
- your program's machine instruction (with your architecture's commads) and OS kernal (adds syscalls that your program can call to do low level OS stuff -- implemented in C as well)
- your source. 

To climb all the way down we: (from bottom to top)
1. How is your program running? OS loader loads a.out onto CPU (contains machine isntructions of your program in a file, and some meta data at the begining). 
2. Where did a.out come from? `foo.o`, `bar.o`, `libc.a` --exec for C lib, `libregex.c`. There was a program (`ld` -- linker), that produces an a.out files 
3. How did you built a `*.o` file? `bar.s` -- another level of machine level 
4. where did your `bar.s` come from? `bar.i`, which is a preprocessoer file, and `gcc` turns `bar.i` into `bar.s`
5. `bar.i` comes from src `bar.c`

### What compilers are there avalabile? 
top open src are currently currently: GCC & Clang 

Hardware manufacturers have their own compilers for example intel -- icc, invedia --nvcc. Those compilers are are hardware specialized, so often their compilations are not portable and they take advantage of certian special tricks in the machine, and are not free. But, often arfe worth they investment (for a company) since they do make the code run faster

### Portability in compilaiton 
Issue: Compiler protability -- the hardest portability problem, since talking of the lowest level.

3 types of protabillity (we say gcc becasue we give some commands and flags, but generally applies): 
- target: what machine will gcc generate for? (can generate for whatever you choose -m32 (for predecessor of x86-64) -mx32 (for x86-64, but in 32-bit mode -- some newwer faster instructions then -m32)). Where will the compiled software run?
- build: what modual version are you compiling with (what version of gcc are you compiling with? or using what version?) (on what machine are you builfing your compiler)
- host: Where will the compilation itself occur (the compiler needs to run on the compiling machine!) 
 
 

#### What does a compiler ussuly assume about machine
What assumptions does gcc make about the machines you are compiling for? 

- flat adress space (memory is just a sequence of bit, each adressed) (not all machines behave this way, EX: tag architecture)
- word size can be 64, 32, 16,..
- are the code and data in the same adress space? (Data to code adressed the same but go to different adress spaces -- can access more memory (because of int size)) -- how does this work? Data and function pointers point to different places.) gcc assumes that code and data are in the same adress space. 

#### How GCC deals with different architechtures? 
We have ARM, x86, etc... How to compile to them all even that they are so diffrent? 

One option is to write $n$ different compilers -- inefficient and terrible to maintain.

Instead, gcc shares as much code as possible. How? using it's own programming language called the *machine description language*. (stored in `.md` files -- not markdown -- language gcc people devised to describe machines). Lists what instructions, what they do, how they can be used -- a manual that the compiler can read and understand. 

When we build, .md helps compile your target 

ABI (OS specifications) is one of the things in the .md files, details on, for example "How do you do a function call in this system?? which is needed since Microsoft and Linux don't agree on ABI (calls with more than 6 args, remmeber?) -- so .md are also OS dependent, not only architecture. 

## Misc 
Can have pointer to functions, apperantly, I didn't know that. The syntax is horrible, but you can do it and clean it up with typdef. (less of a need in C++ because can use all those extra machinery).

### Just in Time compilation using gcc
JIT interface for GCC (libgcijit) -- *gcc as a subroutine*
```
char * someSrc = <some src code>
void (*mc)(void)(machien code)

mc(someSrc)
```
(literally compiling strings to code, in runtime, and running it -- keep the danger in mind)

### `Static` keyword (not in a class's context -- there are none)
create a foo.c file with a static function `f`. No other file can call `f`, another file can have it's own `f`. C does that in the compilation.  


### Exception Handling 
The idea is to make the code readable on the usual case, where exception happens only rarely. 

Complaints: also helps the bad guys figure out what may go wrong. Eggert claims the except block tends to be ridden with error. 

```c++
try{
    x = f(y)
    return g(x)
}catch(...){
    //do error thing
}
```
altarnative without using exceptions: 

```c++
x=f(y)
if(x==EOF){
    return -3
}
```

### `Generic` in C 
a statement that behaves diffrently dependign on the type of the object passed in 
```c++
return _Generic(E,
                float: 1,
                long: 2,
                defualt: 1)
``` 

Often used in macros (there's supposudly an `abs()` example somehwere). 
### C, programmign std (atomic access) RETURN TO THIS, WHAT AM I TRYING TO SAY? 
Run on the bare minimum, no OS is needed. C offers atomic acsess, usefull for threading. 

POSIX, is the OS std that ca

# GCC

### GCC: working with different languages
.c,.cpp,.java,... gcc can compile different languages (different language frontends)

1. Parsers generate rees isns a standard GENERIC data structure
2. GCC-specific phashe (GIMPLE-form), 3 address representation EX: MULT(b,c,t_1), higher level instructions that you can compile into more specific instructions (GIMPLIFICATION)
3. Translate GIMPLE into SSA (single static assignment) -- only assign to local variable. have body of loops get created and get pulled out of context. loop unrolling (if possible, doesn't have to implement i each time). *yah idk what this stage does*