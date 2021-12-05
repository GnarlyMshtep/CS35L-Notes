
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

### From lesson 2, branching + merging + working with upstream 
- `git branch` (list all local branhes)
- `git branch -r` (list all remote branches)
- `git branch --track emacs-27 origin/emacs-27` (I want to get a copy of the emacs 27 branch locally, if someone makes a change, I want to see it locally (emacs 27 is the name of our local))
- `ls -lt` $(find x -type f) (sorry, wasn;t paying attention)
- `git checkout [branchname] OR [commitid]` (change the local files to match a local or remote reposiroty)
- `git checkout HEAD` (last commit on current branch)
- `git checkout -b my_new_branch` (create new branch and point to it)
- `git branch -d myNewBranch` (delete the branch -- gets rid of the lightweight pointer, the commit still exists and is accesable by id).
- git branch -m oldname newname (rename a branch-- good for typos)
- git clone local local2 (get 2 compies of the repositories locally, where one mirror what's upstream (remote))
    - work in local2, and once your'e happy (do work, git add and commit)
    - publish the work using -got format-patch main (what does this do? outputs a patch (text) file that contains all git knows to know what is in this commit and reconstruct it)
- git fomat-patch main mybranch (all the changes from  main to  mybranch)
    - what can you do with those patch files? You can email them to your mates, ask them if it looks g) 
    - Now, after the approval of your mates, cd ../local; git pull 
## Misc 

### .gitignore
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
we have global and local git config files that add variables and all sort of requirements. Hooks at .git/hooks

## Branches

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

    -\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\*-\* (mainline)
        -\*-\*-\*- (release 20 -- slow and conservative)
                -*-*-*-* (release 21, eventually and cautious)

- motivation is to maintain all releases (oh man I did not pay attention he might have said more things)
- satisfy developers who want to be conservative and radical 
- also satisfy users at different levels of radicality (use mainline vs an old reease) 
*another motivation: competing ideas for how software should run*
*another motivation: compatebility with different systems* 
