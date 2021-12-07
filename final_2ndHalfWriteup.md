# Version Control and Backups at a large scope (not zooming on git)

### The different needs of backups and Version control

**Version control needs**: 
- recoever from screwup -- fancy undo (undo something from last week, or a whole set of things, etc). 
- explore history: answer the *why* questions. Why is it the way it (by it we mean a function or some piece of code) is? Why not another way? *An altarnative technology for comments.* 

**Backup needs**: 
- undo massive data changes 
- Motivated by being able to respond to failure scenerios such as:
- ussuly need to go to rough points back in time, not expecting the precision of git versioning. 

    - you lose power
    - flash drive fails (some or none is recoverable)
    - user deletes/ trashes files by mistake 
    - an outside attacker deletes/trashes files (ransomeware)
    - *the hardest problem* inside attacker deleting or trashing files, that is, because he could trash your backups. We will not consider this. 

How to know what failure to respond to? statistics: *Annularize failure rate (AFR)*
Prob that drive fails in a year of uses (use quantification somewhat subjective) 
The precentege is .2%-2.5% per year. 




## Backups

### What do we need to back up?: 
- file contents 
- filesystem data (things other than contents -- metadata (the stuff from `ls -l`))
- `df -h` filesystem orginisation, includes things like paritions (how we divide data), mount points (where the filesystems apear to the users), options (read only --ro). System config information that is too low level to be in a reg ol' file. 
- firmware (when you boot, the system does not run any code from a file, it runs "code" from hard-coded memory blocks), we may want to save that. 

The first 2 are our focus 
### Two general approaches: (1) block level
You don't care what blocks are being used for, just back up blocks (of about fixed size) of memory. can optimize at the block level. 

- Simpler to understand + implement 
- Applicable to all fs.
- misses out on a lot of optimisation chances

### Two general approaches: (2) Filesystem level 
We can backup more inteligently based on knowing how the filesystem works. For example, changed a file? can automatically change the timestamp. 

- More likely to be efficient
- specific to fs
- more difficult to create. 

#### What changes can we ignore when doing backups? (thinking of fs)
- don't bother backing up unused data
- don't backup caches and other reconstructable/preformence-oriented data. (speeds up backups, but slows down recovery). 


#### When do you reclaim backup storage? 
You backup every x time, what do you do when you run out of storage? The dumb strategy is to run them out linearly. The other is to sparse, such that you let's say keep: 
    - 1/day for last 6 months
    - 1/week for last 6 years
    - ...

#### How do you do backups (more) cheaply? (but everyting is a tradeoff)
- simpleton: Do them less often.
- Another idea is backing to a cheaper device (main is fast flash drive, the backup is older disk drive, mag tape (what google does now), optical). Can create a chain of cheaper and cheaper backups for less and less accessed (& important) stuff. 
- Remote backups -- issues of privacy and trust. 
- do them incrementlly: Only save the blocks which changed, or be even fancier and save only changes of files that changed (Save the deltas to files)
    - *how do we express delta?* 
        - in blocks: `[(index, newValue)]`
        - for text file (filesystem level) backup you use an edit script. So like a file of a little language that can be ran to change the lines of a file (commands like append, delete, replace, etc... can be more complicated to optimise). Fancier scripting langugae = smaller backups , but less efficient reconstruction. 
- Deduplication (at the block level): Look at all blocks in you system, some blocks will be very simillar or identical -- we don't need an individual backup for each! Now instead of just blocks, we have (block, [list of indexes of indentical data]). Can be combined with deltas to make it work even for simillar blocks. More subject to problems if the drive fails (has partial failures). In practiacal systems, there's a lower level system that prevents failures (like mirroring). Very common in high-end storage systems. 






### Misc 

#### How do you know your backups are working? 
- unplug a random drive in the server room, that is, test them as realistically as possible. That's expensive. 
- cheaper alternatives -- checksums. save checksums of the way the data used to be, then use checksums (hashs, approximately) that allows you to see if what you pulled is what you put in. 

#### The most basic backups strategy: RAID-1 aka mirroring:
Have 2 drives having the same data, probability both fail is tiny.
- not cost affective. 
- (relatively) Esay to implement.
- Scales linearly with storage. 
 
Note that the combined mirrored chance of failure $\neq{}(AFR)^2$ because the events are not independent. But it's also a passimastic estimate because you replace bad drives and don't wait all year. Operations person will make these calculations routinely.
**GO BACK TO LECTURE THERE WAS SOME QUESTION ABOUT THE PROB OF FAILURE I DIDN'T HEAR I SHOULD KNOW BEFORE THE EXAM**
- mirroring only helps with recovering this moments data. If the user deletes something, he will have deleted it from the disk and it will be recoverable again. 

## Versioning (at the file level)
- files can have versions, for exampele (not fr but could be) `ls -l --all-versions` (you will see file;19 -- saying that it's the 19'th verison) and so on until myfile;1. This direct approach is Rarely used nowdays because: 
- how do we decide when to increment the file version? Every write? too much. We can't decide this on the OS level, instead, let the application control when a new version is created (it knows itself best). 
- how to prune (what is prune?) older versions
- some application will not work well with this system. Like a DatabaseManagemn=entSys which will get to slow 

The OS level approach is used:
- Snapshots: System decides, not applications, when to take a snapshot of every file in the system. (butfs, zfs, WAFL are examples)
- in WAFL (seasnet) -- .snapshots sunbdirectory in every folder. Not perfect, to , let's say, 15min of work lost. can't delete snapchots. 
- sounds slow, but itsn't because of *copy-on-write*.
- a copy of the filesystem doesn't take very long becuase we just have the copy one write. 
- that's what WAFL does and that's why it fast.

### !copy-on-write (CoW)
`$ cp --reflink=always a b` let b just point to a, and need only 1 copy of the file. That is, a and b point to the same inode. Once you modify file b, the sytsem will create a new block for b and then copy all the stuff from a + chnges to it. Like a smart point by reference. (lazy copying)

### WAFL filesystem 
*read up on this, highkey spaced out a d bit and it seems important and interesting*
root points to everything reachable from the root in a tree structure. We differ the work the until we chnage and actually need to. The file system that we go to when we cs on wafl is a tree from another root. Not allowed to change any of the .snapshot tree.

We can reclain all the blocks that are not always reachable. 


### Software Version Control systems (assuming files work)
some examples of things to "control": 
- source code
- test cases
- documentation
- sales blurbs

(common) Limits for such systems are:
- big binary blobs (such as vids or imgs). because their deltas are "weirdly patterned" that is, not simple text where you can tell the lines. One solution is https://github.com/microsoft/VFSForGit
    - in practice, we often don't version those use version control for the stuff it works, then a list of names of blobs for the rest of the stuff wheer teh blobs are kepts elsewhere. 

#### WHat do we want? in our Version Control system?
- histories (can't keep snapshots indefinetly, in sw systems we want those systems to be indefinite). Histories contain metaadta as well as data. Metadata is about filesystem and about the history iteself (who made this commit?)
- when file changed names, What to do about it? (Do you actually replace it and lose the fact that it's really the same file?). 
- last 3 changes made are? (meta meta information)
- atomic commits to multiple files -- change all or change none! More difficult than in fs because in fs we do atomic changes to single files.  
- hooks -- *git doesn't do exactly what you want, often too permissive* restrict changes per project, get things to happen on certain actions. 
- security (but we again don't talk about it) how do you know last commit was done by fellow teamate and not by attacker? Auth changes. 
- format conversion (CRLF vs LF, for example). 
- encodings (UTF-8 vs lesser encodings) -- want everyone to be able to work with the same encoding/their encoding 
- navigation of complex histories (graphical tool or better)


### history of version control 
from (X -> Git (2002)). 

git came out revolting against BitKeeeper (which was propriety code for Linux). Git said let's do the ideas of bitKeeper from scratch, but do it open source! Licensing was the big deal. 

- SCCS Bell labs ~ 1975 (single file vc -- every file had it's own repository) -- the design choice from here that survived is that you can walk through the repository in a single walk and retrieve it any version that you would like. (delta backwards and forward). Can save less and, but O(size of rep to commit from repo)
- RCS (file only) -- save later version O (latest version) for retrievel. **How efficient will it be to grab what I want?**
- CVS (centrakl repositoy )
- Subversion (no central repo)
- Bitkeeper (let's do atomic commits and have no central repository)

# Git 

### What does git do?
- keep track of your projects history (that is, all your previous commits)
- keep track of your projects future (that is, the staging area and the work you are currently doing and will be commited). Eggert claims this is equally important. 

## The git general workflow
1. Edit your file -- changing working files, git doesn;t know or care about them, you don't like? always refert with `git checkout -f` throws away what is now, esser version of reset.
2. `git add FILE1 FILE2` files you want to commit in the following commits. Puts the contents of these files in your future (*staging area*), can keep editing.
3. `git commit` put into the repo as fresh commit, You will be prompted for commit message. 

### Suggested format for commit message
Single line summery of the change, 50 chars is nice. next line empty. Further explanation motivation. In large projects, commits are marketing tools meant to convince other users to pick up the change.  

### General workflow commands, lesson 1 (not lower level/ branching commands)
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

### Stashing 
Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The command saves your local modifications away and reverts the working directory to match the HEAD commit.

Example use:
```
git stash # To add changes to stash stack
git stash list # Shows list of stashed changes
git stash apply stash@{0} # Retrieve stash
git stash clear # Clear stash list
```

can git stash list if you have multiple stashes -- else u apply more recent one's



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
- use `git rebase -i` (interactively) `BRANCH` (programming at a different level -- editing our history)
- `git commit -amends` (replaces the most recent commit with this new commit (with new message)). 
- Rebasing works best when a few changes on ur private copy
- a use case could be: you work on a copy of the repository localy, make some cahnges, but meanwhile upstream has changed. But you worked on a diffrent sector and made small changes that are not very related with the ones done upstream and like buetifull history -- so you you rebase your changes to upstream.  
### Remotes (remote repositories / upstream)
You are developing a program with a lot of other people where you guys are all talking to the same remote repositories. 

`git remote` -- what your remote is, the most common name is origin. You usully get a remote by cloning the reposity from upsream. `-v` flag talls you where the fetch and the push in particular come from. 

Can have more remotes for sub-working groups. 

`git fetch` updates a branch in the local repository to match the remote. 
## git internals
Git plumbing vs porcelin (low vs high level), we want to build high level ontop of the low-level and understanding it helps. 

Notice the overall theme: .git wants to hide and be unobstrusive to our project. 

### The folders & files inside .git

#### .git/branches/
I think, but couldn't find anything about it, that it's an old folder there for backwards compatibality. Ussuly branche head ptrs are stored at `.git/refs/heads`, where there's a file for each head that says it's commit id (and that's it).

#### .git/config/
config -- repository-specific-configuration 
[category]
    key = value 

Easy to mess up if you go editing on your own, should use git commands to do it. Stores things such as 

Example: 

```
[remote "origin"]
        url = https://github.com/GnarlyMshtep/CS35L-Notes.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
        remote = origin
        merge = refs/heads/main
```

#### .git/decription
for gitWeb. Some people like to give names to everything 

#### .git/HEAD 
ex: `ref: refs/heads/main`

When your working, you want to be working relative to somewhere in your repository. That will be the place that what's in your staging area is compred to compare what you got to what's in there." 

#### .git/gitk.chache
caches git info in a gitk friendly format for prior use. Happens after you use gitk.

#### .git/hooks/ -- event handlers
Have written about this elsewhere, but these hooks are scripts that get executed as a response to a certain action happening in the repository. The folder, by defualt, contains example scripts. 

*git pull also uses the lazy copying*

#### .git/index
A binary file which represnets your staging area. (all the changes that you plan to make in your next version)

#### .git/info/exclude -- a private .gitignore
patterns of filenames that you want git to pay no attention to. (why and additional one to .gitignore). Because this one is not shared by other people, it's not part of the history -- only for you

#### .git/logs -- a chache
A cache of recent changes to stuff

HEAD -- log of when and how head was created and moved about, a cache of the last git-things you have done. There also are `/refs` which conatins both heads and remotes, containing branches and the last thing done to them 

#### ./objects 
The big gurrila of git. A bunch of subdirectories with /`dig``dig` to give it better searchabillity and smaller size for the repo on itself.  Each one of those is the full information for a commit.

#### packed-refs
an optimization of refs -- the tips of all the branches and tags. 

### Git's Objects + Comparison git fs with POSIX fs
We have been seeing POSIX filesystem so far and git's fs is sort of analougus. Tree play the roles of directory, those trees contain (refrences to) blobs which play the role of files. Why not just yuse POSIX? There are under-the-hood optimisations for version control. Still many ideas are reused and adapted  

Types of objects: blobs(reg file) tree(dir) commits(symlink + metadata). Premission over here as well. Difference: You can change file contents, but in git you cannot change file contents -- they are all immutable -- you are tryna record history -- it shouldn't be allowed to change. (a lot of optimisations accesible) 

#### ObjType1: Blob 
Just a bytestring -- git doesn't care what's in their. Just ordianry information. 

#### ObjType2: Tree 
Branch node of a tree of objects. Roughly corrosponds to a directory, maps names to objects. *Could actually be any graph, not only tree*, but he didn't explain much. 
Contents: 
There's a tree for each commit -- a commit is an important tree. 

Mode, Type, ShaID, name_in_this_tree (Type is cached in the tree for preformence reasons. )
```
100644 blob fa5296614a8b937aae62d469c2abdb697021cbc5    LICENSE
100644 blob 7e9da7436d431e73911a16fd2b26c7ba9d16b089    README.md
100644 blob 84a04be2229c4e4517b2b26bff4d8fc6937ac1a2    discussion0 -- notes.txt
100644 blob ea81e6669ab9ecc1974ffcb500fa5ad27d8b591b    discussion2_10_8_21.md
100644 blob 7fa4a1fcfa1db15a9c0b69a9853d700ff8ca88d6    discussion3_10_15_21.md
100644 blob d215fb4d7d074cd5596ac101097a4f79b9fba6cf    discussion_#_11_5_21.md
100644 blob cdd908ddd2d678b76a9da73bd5da42151db2b4c8    discussion_1_10_1_21.md
..........................................................................
```

#### ObjType3: Commit
representative of a state of a software development repository. Has parents (typically one). Has a justification -- *answer the WHY questions*. Supposed to give you meta information that isn't in the comments. Motivation for the tree. 

#### Compare and contrast POSIX and git fs
- commits which are analogous to symlinks have much more information, and as a whole, the git fs allows for much more documentation. 
- directories are much like folders, blobs are much like files
- in git everything comes with a hash value to retrieve it since it is a "is a content-addressable filesystem."
- git objects are compressed by defualt to save space. 


### Some Plumbing commands: 
1. `git-hash-object`: hash what's passed. 
    - use --stdin to read it from stdin 
    - use -w to actually write it to the git objects -- can use to create new obj in database. 
    - doesn't care about what's the filename or anything just the content 
    - smart enough to not store 2 copies of the same data in the database (prob using above)

2. `git-cat-file`: output an object by it's hash 
    - -t to print type
    - -p to print the contents 
    - git-cat-file hashOrTagName^{tree} -- prints out the tree at that commit (ex above at tree, use tag main) 
    - swiss army knife for exploring git contents. 
3. (create a tree) `git update-index --add --cacheinfo "100644 (mode, readwrite to me, read for anyone else) ,hashashash, filename"`. Then, do a `git-write-tree`
    - Updates you plan for the future (stage stuff). 
    - Built a tree, but it's not mega useful since it's not an important tree like a commit. 

4. `git commit-tree hashOfTree` (then write commit message until you write C-d) 
    - suggestibally, commits should have a nice short line, then a blank line, then, more info)

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


### Git bisect 
you have a linear (or maybe doesn't have to be linear, possibly does not) change of commits and a bug is introduced somewhere along the way. We have a script that returns with status 0 on success. We can binary search through the linear chain to find the commit that broke the sytem. Assumes we have [nonbuggy]|[buggy] split in the history. This is not always true, but running bisect may give us some emore useful intel

ex use: 

```bash
git bisect start FROM_COMMIT TO_COMMIT
git bisect run sh -c make && ./testScript # it will take a while possibly because of make, but yep this will works. 
```
can `git skip COMMIT_ID` if you skip commit that may not give us solution.

we can also run without a script and tell ourself if something is good or bad (git will binary search through)

Ex of no script run:
```bash
$ git bisect start
$ git bisect bad                 # Current version is bad
$ git bisect good v2.6.13-rc2    # v2.6.13-rc2 is known to be good
```

*how to do for more complex history*: 
(it works but the inner algo get more complex) -- prob works but he doesn't know


### .gitconfig
we have global and local git config files that add variables and all sort of requirements. 

### Hooks (also in intrenals folder contents)
Hooks are certain scripts that git can run when certain actions take place. In `.git/hooks/` one can find all sorts of sample scripts. They are marked `something.sample`, where `.sample` tells git not to actually run the script. They can add all sorts of custom behivors for your script.   



# C, the languge, not it's compilation
C is the lowest level software tools (programming language) that runs everywhere. Created by Ken Thompson and Dennis ritchie in the mid 70's at Bell labs. 

### What is C used for today? 
- rarely used for end user applications, C++ (and higher level friends -- python, etc) are more common for end users. and for that reason, it's usage is declining.
    - although, it's market share is declining that may be just because many more end user applications are being written -- not necesserily less C applciations.
- When C is used, it's primarily for components/auxilary tools for software developments or embedded devices. 
- when your application must be fast and efficient, you go to C, but you also pay a cost in development time (C is primitive and u do everything) and security (due to low level mem stuff) that's why people go down to it only when they have to. 
- One example is Python, another is the Linux Kernel (Big C program that run under all ), Emacs,... 
    - when something is used very often we care about it enough to invest the time to write it in C, overcoming slow dev time and carefullly checking out errors. 


C++ was originally a C compiler. C++ implements all those features using low level tricks on C. 



## Misc 
Can have pointer to functions, apperantly, I didn't know that. The syntax is horrible, but you can do it and clean it up with typdef. (less of a need in C++ because can use all those extra machinery).


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

# Compilation, particularly with gcc with respect to C (not optimisations, just the steps, protabillity, how it works)


##  Compilation in C (some GCC specifics)


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




# GCC compiler options + program optimization using compilers (both speed and security)
selecting a different subset of flags changes the envieroment and may expose (or mask) bugs in your code. 

## Security/Correctness optimization using compiler
Low level langs have a high security bugs on how to break into C/C++ programs. Security doesn't only mean that we don't get attacked, but also that unexpected things do not happen. 
*stack overflow attack is an example* **return to learn what the stack overflow attack is**

### Example: Stack overflow attack and it's solution the `-f-stack-protect`
somwhat less efficient, but does checks
    
    What GCC will do is make our arrays larger at the begining of the begining of the stack. Puts canary at the begining of the stack.
    `int canary = R (some random value)`
    just before we return make sure that the canary is equal to R. Now attacker needs to make sure it finds the canary with R. 
    if index over the canary.

    Turn off with `-fno-stack-protector`
    (controversial)

### CheckType1: Static Checks
**see more examples at the debugging chapter!**
Static checks are the one's that the compiler does at compile time, they make your program more reliable. They are challenging for the compiler since it doesn't know what is the value of any variables and which specific execution path is taken (where an exhustive search is infiesable), so it will have to use heauristic. As such, it has the follwing up/downsides
- `+` zero runtime cost 
- `-` slow down compialtion 
- `+` some errors (such as derefrencing a null ptr) can only happen and can only be caught at runtime
- `-` less precise than runtime checks. 

#### StaticCheckType1: Intrusive static checking
Diffrent ways to tell the compiler -- check this while compiling. Intrusive because you have to change your code. Makes a lot of since because we have knowledge about the execution (and expected execution) of our code that the compiler does not, and if we were to communicate that with it, it will be able to act more intelligably. Eliminates the need for runtime checks (see ex) Another + is that it's a great way to document our assumptions (see example). 

The std way `__static_assert(C)` where C is any condition but something you can eval at compile time, for example: 

```c++
__static_assert(EOF==-1); //make sure that EOF is what we assume. Also documents the assumption we made. it's faster, than something if-else check (and only happens at compile time). Spelled this way in C2x (next version of c std). in current version of C: 
_static_assert(C,"ouch");
static_assert(C) //in C++

/*some code assume EOF == -1, this is always true on linux, maybe not always elsewhere*/
```



#### Non intrusive static checkking 
**see more examples at the debugging chapter!**

Are ones that you don't need to change your code for. 

`gcc -Wall` -W is common, `-Wall` says turn on ALL (the warnings that the gcc guys though were useful enough often enough) the warnings. 

Exampes of specific flags:
- `-Wcomment`, a comment not always properly closed such as `/* x2 /* Y */`, which is technically valid, but perhaps wrong on context (undefined behivor)
- `-Wparntheses` for arithmatic expressions such as: `a\<\<b+c`. Do we take `a` into `b` first or add first? Another example is `a & b| c`. Both these cases have actual answers (for example, or actually has priority), but the gcc compiler guys think there's a good chance that you don't know it and goofed up. 
- `-Waddress` Lets you kwow when comparing address with pointer such as str == "doc" where str is `char *`, again, here gcc estimates that you didn't mean to do that and are messign up.
- `Wstrict-aliasing` is a contreversial example seeing that Linux Kernal devs use these tricks all the time. As it says it makes you dereference very strictly, where without it we can do all sorts of mem tricks (look at only last 32 bits on purpose, set the last 3 bit to type code, etc). 
```c++
long l = 27;
int *p = (int *)&l; //pretend 64 bit qunatity is 32
return *p
//return value depends on endiness of machine 
```

Note that *warning does mean that your program is worng!* (and it'll still compile). When you get such a warning you can either adjust your code or turning warning of with `-Wno-parentheses` or address, etc.  


## Preformence (mainly speed) optimization using compiler
Q: Optimize for what? You can optimize for different things (memory usage, readability, debugability,speed, we already discussed security). `-o` is bland optimization that is useful for all and nonoffensive. 
Why not optimize by defualt if it is so nonconversial? gcc runtime costs.


### Non-intrusive preformece optimisations with `gcc -o` and friends
build speed and optimisation level are inversly correlated. During dev, we might not care about optimization as much as gccs runtime, particularly on large projects which may take mins-hrs to compile.  

#### Levels for optimisation for time (levels)*: 
1. `gcc -o2` -- Eggert's sweet spot
2. `gcc -oN` for N increasing

The more optimized the less debugable since it becomes less predictable. These opmisations are not supposed to introduce bugs (although they may certainely expose them), but again, less reliable and more unexpected the more you optimise. At a certain point, gcc can't optimize anymore, no matter how high N is. (even -o3 has been documented unreliable and not always as good as o2)  

#### Optimzation for space*: 
1. `gcc -os` optimize for space

Important for embedded devices

#### Other varient of optimisation
1. `gcc -og` optimize for debuggability (with some speed involved as well -- many optimizations make really confusing machine code (for example, GCC may choose to not put a backtrace on where the current stack frame came from, if it thinks it won't return)
2. `gcc -o0` no optimizations (perhaps less optimizations than regular)
3. `gccc -fltc`: link-time optimization (not optimizing the link time, but the code).
        What does it do? Instead of compiling all *.o to optimized machine code, it writes it to a high level description. Then As such, instead of optimising with respect to each .o file it optimises with respect to entire codebase. `lto` the link time optimizer, takes a long time.   

### Intrusive preformence optimzation
intrusive = gotta change your src code. Tell your compiler how your program runs (now with speed instead security -- failchecking)

#### `__atribute__` for function optimization:
giving pragramtic preformence advice to compiler. *(non portable -- many of these commands are recognized only by gcc compiler)* to make portable (cancelling the optimization for other compilers bu tallowing them to compile) use:
```c++
# if ! __GNUC__
    # define __atribute__(x)
#endif
//If not GCC, make attribute a macro that expends to nothing, that doesn't affect compilation of that code
```
Here are some examples of such optimisations: 

- the cold/hot attribs:
    ```c++
    void abort(void) __atribute__(({cold,hot})) // indicate if its (un)likely will call abort (unlikely in this case, we call it once!)
    //can put code for calls far away to not waste valueable cache space
- no return 
    ```c++
    __attribute__((noreturn)) //abort never returns as in, like not a value, but to return out of it. (can just jump to it, but never need to put it's return value on the stack)
    //so popular it was standardized in C, and can be written:  
    _Noreturn void abort(void)//... 
    ```

#### `__atribute__` for data optimization optimization:
*optimising type request:* A common thing to do is ask what object type are we pointing to. A slow thing to do is to derefrence the pointer, and go to the object where the first word is type. Now, every time we just need type we get the entire object, which is wastefull.

Alternatively, we can use the last 3 bits of the pointer from pointer to tell us what the type. The last type is "other." (doesn't pullote the cache). The example did that 
```c++
(long) p* = //something 
//we tag by adding 3, and when we need to derefrence we untag. 
// works if we can assume that the last 3 bits are 0, for which we need to allign (since mem adresses don't go to 64 we can assume that)
//then to get back type:
(char*)((long) p & ~7) to undo the last 3 bits
//!okay this is actually kinda beatifull!
```

Another example is alligning variables (remember this is important and faster from assembly). 
```c++
#define SIZE __attriute__((aligned(8)))
char arr[SIZE]
// compute stuff into arr if(strcmp(arr,"ABC")==0)
// make sure that things are on a multiple 8 -- faster comparison 
char *p = &a[0];
long l = (long) p;
long tagged_l = l+3; //(to create a type of 61 bit val and 3 bit tag)
```

#### speed optimization `__builtin_unreachable()`
The `__builtin_unreachable()` tells the compiler that the function tagged `__builtin_unreachable()` will never be called . What's is it for then? Any call to this function is a message from the program to the compiler that this line cannot be reached, giving the compiler advice that this part of the program must not run. Example: 

```c++
#define assume(x) ((x) ? (void)0 : __builtin_unreachable())
//lets us tag an x s.t compiler can assume that it is true. 
int g(int n){assume(n>=0); return n +n/2;} 
// code generated is now faster since the compiler can do faster arithmatic operations that only work for positive n   

//could make a function, but worse 
static void assume(int x){ if(!x) :__builtin_unreachable}
//macro is better, works for all types. 
//(void)0 is a something that doesn't return, so you don't asy if(assume(x)). 
//not generating any machine instructions, just telling the compiler that it can assume this. It wouldn't make any __builtin_unreahable calls.
```

### Smart pre-optimising: Program Profiling 
Look for hotspots in your program (statements/funcitons that get executed a lot). Then, use outside knowldge (say, in algorithms) to optimize the code as fast as much as you can. Allows you to focus on the essence when optimising. Can optimise then profile again. Has a potential for larger optimisations since radically changeing the src where it matters most.  

`gcc -coverage foo.c` count the number of times each statement gets executed. (Not as slow as you think because can only count the function entrences and exists and conditional branchings.) Outputs on a bunch of binary files. use `gcov` to analyze that. 


## Misc 

### Just in Time compilation using gcc
JIT interface for GCC (libgcijit) -- *gcc as a subroutine*
```
char * someSrc = <some src code>
void (*mc)(void)(machien code)

mc(someSrc)
```
(literally compiling strings to code, in runtime, and running it -- keep the danger in mind)

### GCC: working with different languages
.c,.cpp,.java,... gcc can compile different languages (different language frontends)

1. Parsers generate rees isns a standard GENERIC data structure
2. GCC-specific phashe (GIMPLE-form), 3 address representation EX: MULT(b,c,t_1), higher level instructions that you can compile into more specific instructions (GIMPLIFICATION)
3. Translate GIMPLE into SSA (single static assignment) -- only assign to local variable. have body of loops get created and get pulled out of context. loop unrolling (if possible, doesn't have to implement i each time). *yah idk what this stage does*


## Example of the unexpected results of optimization
```c++
int n = INT_MAX;
int p = n+1;
if (p < n ){
    return -1;
}
```

The compiler will assume p>n and optimize and our check for integer overflow will fail. This is technically our fault since behivor on integer overflow is undefined, but without optimisatrion, it may have checked. 




# Encoding + Compression 

Taking some data, preserving the same semantic information content but representing it with less charecters (bytes). Information Theory gives the rigourous limits/ideas behind this task, but we'll just focus on some practicallity. 

When we compress data, we encode it in a more efficient way. 
## Compression techniques


### Huffman coding
You want to compress. Input is divided into symbols from a finite alphabet $\Sigma{}$. Represent textual input in more precise way. We use the whole unique prefix shananigan thing to encode AAAAAAAUGH into $0000000010010111011$, which does save us many bytes. 

The problem set up is we get charecters $c_1,...c_n$ with frequences $f_1,...,f_n$ in our data. Come up with a good set of bit strings to encode each charecter. Huffman proved the optimal strategy to come up with the best possible way using a binary tree which resents the encoding, where, taking a different prespective to look at the situation, we want the average traversal to have minimum depth of search. 

how to: 
1. take the least used chars and put them at the bottom of the tree. Fake-abstract them into a single charecter with their combined frequeces. 
2. Play the same game again, where we consider the combined charecters to be 1 charecter and continue combining subtrees.  

That's assuming that we know the frequency table a-priory. What if we don't?


#### Huffman coding no apriory frequencyies: static approach
Note that in some situations we can just assume an apriori frequency table. That is, if we have an english text we can use a table found to match in general for english text. Not the optimum but good approx and saves trouble.  

The easiest approach is to scan over the entire text to be encoded, generate the frequency table, (possible communicate it to whoever might need to decode). 

- `-` two passes over the input (first to find frequencies and then generate encoding).
- `+` generates a provably more optimal encoding then the dynamic option.
- `-` must store (and have) frequency table to decode.
As such, we might do something like this when we have a lot of data we really care about being compressed well. 

#### Huffman coding no apriory frequencyies: Dynamic approach
Compressor initially assuming all symbols are equally likely. After the first symbol we have a modified frequency table, where that symbol has higher frequency **is it one? that doesn't make much sense**. As we encode the frequency table becomes better, and so does our encoding. A decoder plays the same game and updates it's frequency table as it goes along to decrypt with accordance to compressor.


### Dictionary coding
Assign an output symbol for some words (that come up often) then huffman encode those! We will spend more CPU to reconstruct but (suposudly) save more space. 
 
The sender sends a dictionary of words (and their appropriate encoding) like {the:\; 0001,...}. For punctuation (and small minucha of 2 very simillar words we don't want to encode seperately) we may have to escape and drop to ascii for the small change and then back up. 

#### Dictionary coding: issues with static approach

The static approach may work for english, but not java. So Dynamically, both sender and recipient start with empty dicts and don't ship dictionaries, just ship ... **didn't catch that**. There's a lot of params to mess with: 

1. what's the length limit on the words (we may encode a repeating sentence with a single eymbol)? 
2. Dictionaries may get really large -- exhust RAM or become inefficient. We may have to dict dome stuff as symbols, but the rest as letters -- Where's the cutoff?

#### Dictionary coding: dynamic apprach taken by gzip; sliding window scheme
gzip and friends don't build a dict for all the inputs and instead use a *sliding window scheme*: 

start      | window |<-here          end
--------------------------------------   (the str)

The window determines the dict! that is, given a word w, if it could be found, we give it's index in the window. Now we have a bound on the size of a window and the size of a w. w size = 64k (or whatever).

gzip uses dictionary coding to generate symbols and frequencies which it huffman codes, all dynamically (zlib does about the same thing). 

Property: *a single bit is flipped -- you're toast -- recipient will begin generating wrong dicts and your done for it*

# Methods to debugg (find bugs) but Avoid Debugging (with GDB or simmilar) 

Debugging is an *extrememly inefficient process* that you should *avoid (almost) at all costs.* If you can come up with a practice the minimzes debugging over almost anything else, do it. If it's not possible to wholy eliminate, try to make bugs easier to find and fix, **make your program fail loudly and reliably**. 

Can be seen as the prcess of finding the right $\delta{}$ of where you want to go from where you are (what can't be seen that way?)

We focus on the buggiest platforms (C/C++) becouse knowing how to debug them implies we'll be able to debug most other things. 

Note that the warnings we will present have no certainties, the probabilities they work are high and increasing with time as more edge cases are found, but never certain. Where these errors are helpful will be due to what you are developing and what for. It is common to include the warnings that are needed in the project standard (included with what comment style, what indents, etc). 

**more information in the GCC Compiler options -- there's overlap**

## Static Checking 
Checking which occurs during compile time.

Limitations and benefits: 
- certain bugs only occur during runtime and it's not feesable to check the running of many (or any depending what the program does) input combinaitons.
- pretty good about the bugs that can be elminited, can target whole classes of bugs. 
- doesn't make your program slower at all
- makes compilation slower

### Nonintrusive (compiler options)
- `gcc -Wall foo.c` turns on a lot of static checks, amongst many other things
- `gcc -Wstrict-aliasing foo.c` warns of using ptrs for the wrong type. The issues that can arise are machine independent depending on types. 
- `gcc -Wmaybe-uninitialized foo.c` warns when the compiler "cannot prove to it's on satisfaction that you set a variable before using it.` Not needed in other languges (like Python and Java where everyhting is initialized of the get-go, BUT, it's still useful because even though compiles -- it will probably output wrong. 
- `gcc -Wtype-limits foo.c` -- not on by `Wall`. when u say something like (u<0) where u is an unsigned produces an error.
- `gcc -fanalyzer foo.c` expends function from function call inside the first function to see if it can find errors that way. Makes gcc wayyyyy slower, but is able to catch bugs that you wouldn't be able to otherwise, see below:
 
```c++
int f(void){
    int v;
    g(&v); //gcc assumes g initalizes v, but it only uses heauristic and doesn't full check. With fanalyzer it will check that g actually intializes v, and else will err. 
    return v; 
}

```

### Intrusive (giving compiler advice)
Annotating your program to indicate how it's meant to run and help the compiler catch errors.

- function decoretors (standardized ussuly)
```c++
_Noreturn void abort(void){
    //can help the compiler generate more efficient code -- (don't push return adress on the stack). Also, makes it complain about unreachable code. I mean, of course it's able to help more with extra info.
}
```
- GCC function attributes (less standardized)
```c++
int hash(char const*) __attribute__((pure)) //looks at string contents derefrence form mem. 
```
This attr tells gcc that the return value depends only on the current state of the machine and that this function will not change the state of the machine. It's a pure function. It has no unobservable affects outside of it. 
- `int square (int) __attribute__((const))` same as pure, but doesn't depends on outside storage (can't derefrence ptrs), just on input.
- `int errprintf(char const * fint, ...);` Literally `...`, not being negligent. Add `__attribute__((format (printf,1,2)))` First is dec 2 all the other args. You are saying your function is declared on the same argument scheme as printf. [This is a somewhat between explanation](https://stackoverflow.com/questions/2735587/in-a-c-function-declaration-what-does-as-the-last-parameter-do)

## Dynamic Checking 

Check one execution. *Does not check all possible input cases.* Cannot entirely eliminate a class of bugs like static checking can. 

Ops tools, not dev tools.

Examples:
- your first defualt, always, is to *debug with print statements!*
- `ps` command, or even better 
- `top` run ps every x sec (5 by defualt here) run your program and look at ps and top to see what mem, itme your program is taking. 
- `time` Takes any program as an argument, tells you how much time the program took. Simple but helpful. 
- `strace` runs your program in a special envieroment where you log all sys calls. 
- `ltrace` traces calls to libraries (like Math.cos or stdio.printf). 
- `  valgrind --leak-check=yes myprog arg1 arg2` like a debugger that you don't need to run into and runs and gives you errors automatically. I found very useful!

*You often don't need to drop into the debugger, look at the logs we produce with above or you auto produce* 

### Runtime checking enforced by the compiler
All the options are here: [GCC page on runtime checks](https://gcc.gnu.org/onlinedocs/gcc/Instrumentation-Options.html)

- `gcc -fantasize=address` Make sure that your buggy program *crashes mroe reliably* bad ptrs make ur program flaky -- sometime fails and sometime does not. We want to fail, and fail loudly! 
    - More precise then valgrind since valgrind runs on already compiled executable, but gcc has access to src!  
    - more code generation for this. (then recompile for full speed, which sometimes is a big deal).
    - valgrind was there first 
    - you run whatever gets compiled form this and it'll fail louder
- `gcc -fsanatize=undefined` catch undefined behivor by the c standard. CLassic is signed integer overflow. (ex: for loop that starts with intmax and goes ++)
- `gcc -fsanitize=leak` mem leaks, as it sounds. 
- `gcc -fsanitize=thread` Threads step on each other. Tries to catch race condition bugs (a shared variable between threads is watched closely). One of the toughest bugs to reproduce and find. Make buggy condition crash right away 

*The goal of all of these things is to **crash realiably***. 


### Change your codebase -- choose platform wisely
- If your technology (language) crashes your program often (C++ is crashing) pick a good (safer) technology. For example, change to python! (or something)

### Change your codebase -- Defensive Programming
write code that assume that the other modules don't work. That is, call a function and expect a result -- check that you got that result (even when it's in the specs and you know the person who/ you wrote the function). For example:

- Keep traces and logs (figure out what ur program did).
- checkpoint restart (at a certain point, your program saves its entire state to permenent memory) Then your program can be recovered from that state (or observed to see if err occured before or after or help to see if anything failed)
- assertions `assert({cond})` by `#include assert.h`. -- runtime check that condition, else crash -- fail loudly. 
    - There's a `gcc -DNDEBUG` to compile without asserts, which allows us to not have them and suffer from their efficiency decrease. 
- exception handling
- *barricades*: you seperate your software to internals and externals (internal is ussuly the most of it, externals is ussuly like the input from a user or some place that is very unverified). The code between the external and the internal is a baricade and must untaint the external data.
    - tainting: a variable/obj that isn't trusted. Something about it could messed up formatting-wise. You have runtime check to make sure you don't pass tainted obj into the internals
- interpeters: execute forgein programs that need to be executed inside a jailors.
    - webrowsers use this approach to run JS. 
    - can be and is done in larger applications
- virtual machines: an interprter for machine code that is very efficient. Often the same arch as you are running. Jailors at the hardware level often useuful as well to prevent crashing. 


## Aside: error fault failure, programmer terminology 
- error: mistake in the programmer's head
- fault: a possible way that something can go bad, but it hasn't happened yet.
- failure: an observable mismatch between the behivor of the program and desire of users/spec

Imply from bottom to top (a failure is resulted by an error and a fault, an error may not imply there is a fault or a failure, etc). We want to minimize failures and do it by reduceing faults and errors ahead of time

# GDB and debugging with GDB
Sometimes it's inevitable and we must debug, and we do, it's important to identify the failure, stabalize it (crash realiably!) by finding a test case that reproduces the bug consistently (which could be hard, with all sorts of mem errs, for example).

## The GDB debugger
WHen your program is debugged with GDB, it runs in its own process (like usual), but another process running GDB is actually "in charge" (the OS notifies it if something interesting/stoppage/unusual happens). GDB can modify the state of your program and process of the program in almost anyway it wants (as we'll see). This gives *GDB lots of power*, which is why mac OS turned off some of the commands which it uses to debug -- attacker often use GDB to mess with your system.   

A plus is don't need to change program at! you don't even need to be the one who compiled the progream!  Yet, GDB works best (most informative) if you *compile with debugging symbols*, that is `gcc -g foo.c`. It doesn't change the executable, but bloats foo.o with debugging info (such as: the names (u gave to) local variables).

optimizations such as `-0N` make your code less compilable because the optimizations make your executable far less predictable and corrosponding directly to code. It may look as if the program counter is moving around errtically in the src code since optimisation change order of actions for some minute machine code optimisations. *Beginner suggestions: use `-o0`, then debugging will be easier.*

### (many many many) GDB commands
More here: [GDB commnds](https://web.eecs.umich.edu/~sugih/pointers/summary.html)

*mostly get execution info:*

- `gcc -g -o memsim memsim.c` prepere your program for debugging by compiling with debugging symbols
- `gdb` to start gdb and get the `(gdb)` prompt, then `(gdb) file executableName` to start debuggign this executable
- `(gdb) run a b >/tmp/out `
- `(gdb) ^C` -- regain control 
- `(gdb) bt` -- get backtrace 
- `(gdb) up` (turn gdb's attention one level up in the backtrace)
- `(gdb) p EXPR` -- evaluate the expression and print result (can compute variable value)
    - p exit(100) -- this will just exit -- this a powerdul expression. 
    - executing code in the debugged program
    - how implemented? GDB compiles some machine code, put it in mem inside debug process, and then (change the instruction pointer) tells computer continue executing 
    - legitemately modifies the running program.


*mostly simple navigate through program flow:*

- `(gdb) stepi` (steps over a single machine instrucitons)
- `(gdb) step s` (single steps through the src code) -- through machine until it find src line number is changed
- `(gdb) next n` (single step through the current function, within a funciton, skip through all of of other funciton call as if it were a single instruciton, let it go and then return as a single line of f, the step would step you in g) helps you *operate within an appropriate level of abstraction when debugging*
- `fin` (finish execution of f) 
- `(gdb) rc` (reverse continue), let's let the program run backwards until we reach the most recent break points. Avalabile on some platforms and on request.  
    - not done by running backwards in hw, but it's done in software. 
    - It does it by recording the execution history and stepping back on the execution history (like in this step we changed x to 5, etc.. must maintain the history of the program to do that, which is expensic )
    - slows things done when you request
- `(gdb) checkpoint` (saves entire state and gives you id)
    - cheaper version of rc that you can use to return wherever you'd liek 
- `(gdb) restart checkpointID` (fo back to ID printed by checkpint)

*even more commands*

- `(gdb) set cwd /etc` set the cwd
- `(gdb) set env PATH /usr/bin:/bin` set some env variable
- `(gdb) set disable-randomization yes/no` (normally, libraries and objects get put into different places as a security meassure, making it harder for atteckers) turning it off makes it easier to debug. This returns to our point from last lectures of reliable crushing (more realiable crushing when the program is the same). This also connects to *reproducubality* make the same output at the same time. Make debuggers such that psuedorandom gens give the same answers. This is by defualt on (as in randomization off_! **This is an example of how debugging your program changes it's behivor.** (what are some other examples)
- `(gdb) attach PID` We say this when we want to debug a running program (such as a running emacs we would likee to debug). 
    - security implications: When attacker breaks in they can run a process as you and run GDB on it. if you break into one program you break into them all. 
    - ubantu (and others) doesn't let you do that anymore. 
    - need to somehow overwrite system security to fully debug/proc/sys/kernal/... 
    - **security vs debuggability** be cautious! 
    - complete control over program's brain 
- `(gdb) detach` (running on it's own + whatever changes we made to it)
- `(gdb) b read_key_sequence` (putts a breakpint at a functionNAme/ lineNumber)... GDB modifies your program by putting a function at that place to stop and give control back to gdb 
- `(gdb) c` (for continue, emacs continues to run) 
- `(gdb) info breaks` (all the break you have currently set in your program)
- `(gdb) watch EXPR` keep checking the valur of this experssion, and if it changes, stopp. Break point for data instead of program.
    - as it sounnds, we have a one big issue: efficiency. How can it do it without single stepping and calling the expression every time. 
    - it turns out that for single variables there is hardware suppurt for watching (like) 4 mem location up until change. 
    - EX: watch (0<=i && i<1000) (print i first tho, to make sure that it's in range).

### Aside: GDB outside cntrol 
- API for controlling GDB 
- GDB has it's own macro language that you can use to write little GDB programs you can use to execute a bunch of commands you often do. 
- Write GDB commands in Python!

Very programabble! 

many years of debugging, many common things you will want to do debugging 

### Aside: Why debug w/o src? 
- screwups -- you lost the src 
- bankruptcy -- lose src, or lose compiler. 
- reverse engineer code by competitor 
- forensics -- try to debug program an attacker is making hard to debug. Prof. Sahai: provable program obfuscation 

# Building programs and checking for portability 
Introduced in the context of needed to build and then debug

The tools that emacs requires to build (several levels of make)
- *automake*: you specify makefile templates, Makefile.am (which is the file that the dev writes by hand). We need to prepere to use make. Automake generates Makefile.in 
- *autoconf*: Makefile.in is ran by autoconf to make Makefile (which is platform dependent)
- *configure*: automatically generated super long shell script. 

What are the audiences for these different build tools? 
- the devs: assumed to have git, automake, etc, all debugging tools) -- superset of everyone's tools. Generate portable files. 
- builders: Simpler set, the shell, gcc, make, ... Generate executables that are platform dependence. 
- packagers: take the output of the builders and will generate packets ready for installation. Generate something like emacs-29.3.deb, which you can install on ubantu. 
- insatllers: ussuly ops people.
- testers: std is to run `make check -j5` -j5 to keep running cpu and not wait for IO. It's common for packages to have built in tests

Not uncommon for large projects to have the src for the file that the use to build their project, and to generate more software to help them build. 

### Why several levels of makefile? 
In one word: standardisation. There's a POXIX spec for `make` (pretty basic since it's teh intersection of all the makes out there). Doesn't have many necessery features -- not enough for ease of development. 

Solution: a boostraping process, a few level of abstraction that make it more convinient, giving you more features. 

a developer should be able to write src files that are incredibely portable all around. The price of that is the cumbersome build process.  

### Emacs build phases
- `./autogen.sh` (checks build tools, runs automake, etc) (DONE BY A DEVELOPER)
- `./configure`  (cehcks platform, allows you all sorts of configuration) (BUILDER ussuly deals with). Generates makefile and a config.h 
- `make -j` build emacs executable
- `make install`. Don't have root? use --prefix=$HOME/prefix (or any other spot that is yours and is not the general home )

### Aside: portabillity checking
For example, our application must run on many browsers and many servers. You can put your foot down and say (I only do this!) but people prefer portability (PPP). 

How do I check that my application is compatible with different {versions of node, browser, etc}. This obvious solution is inefficient since overall is: numBrowser*numNodeVersions. That's a lot for a 2 component application! (where there ussuly are many). Grows about exponentially with number of components. Too many things to test exhustively. 

Even low level apps have portabillity issues (32 vs 64 bit, x86-64 vs AARCH64)

Some techniques are random sample
- random sample
- bother testing only the best versions
- compatabillity guides
- automation tools
- many examples in webdev

