# Github

## 1. Intro

Version control - system that records changes to a file or set of file over time - we can recall specific version later 

Local VCS - on computer 

Centralized - server where we operate with code 

> we have central server where code is managed
>
> each programmer sent from its own workstation 

Distributed - each developer has its own local copy - > we push them to remote repo 

> each has local copy 
>
> send updates 

**advantages**

- collaboration - track through beatiful distributed graph theory tree model 

- preserve all versions

- rollback to stable version 

- updates bugs - > we can check who did it 

- snapshots

  ​	- Git records changes made to file rarther than file itself  

	- every task is un-doable
	- no changes without git recording it 
	- every collabo's working dir is a branch by itself 



**how does it work ?**

1. we have our dir (local)
2. we did some changes in file and then we **add to staging area**
3. then commit to local repo 
4. git push to remote repo



We can pull from remote 

## 2.Git setup

once per given computer - they'll stick around between upgrades 

tool ```git config``` - get and set config variables 

to view all of settings :

```bash
git config --global user.name "Piotr Sadowski"
git config --global user.email "programatorseu@gmail.com"

git config --global init.defaultBranch main

git config --list # to see 
```

## 3. Git basics 

### 3.1 How does git Store information ? 

> it is key-value store

- **value = data**
- **key = hash of the data**



folder name + file name = hash of file = git key 

Hash is generated based on contents of file 

Hash - always have fixed length 

- one-way function ( we could not find input what was used to create hash)

- the same input will create the same output

  

SHA-1 (160 Bits) - git uses it 

- it is represented in Hexadecimal chara ( 40 characters )



### 3.2 Commits

points to tree.

Contains metadata : 

- tree ( root directorry) / author and comitter / date / message / parent commit

`sha1` of commin is hash of all this information 

```bash
echo 'Hej' > hej.txt
git add hej.txt
git commit -m 'Initial commit'
```

>[main (root-commit) 4311eb5] Initial commit

```bash
tree .git/objects
```

`git cat file -t` // print the type 

```bash
git cat-file -t 4311e
```

>  commit

```BASH
git cat-file -p 4311e # print contents of file
```



>tree fcf9de89a6141f5f1b3bf2c06f7ea52f1d2ed02f
>author Piotr Sadowski <programatorseu@gmail.com> 1659934226 +0200
>committer Piotr Sadowski <programatorseu@gmail.com> 1659934226 +0200
>
>Initial commit



>.git/objects
>├── 43
>│   └── 11eb578b25d77e278612b2834c264f7eec15a9
>├── 75
>│   └── e5d44eea92d8287ef17bea46c30da2487d3fe6
>├── fc
>│   └── f9de89a6141f5f1b3bf2c06f7ea52f1d2ed02f
>├── info
>└── pack

**git object types**

- blob

  > git store files / any file with extension .jpg ..

- tree  

​		- store information directories & files 

- commit
  - different version of projcects
- tags

		-	 text pointer to tags

if we change any data about commit - commit will have new sha1 hash

even if the files do not change - created date will. 





**references - pointers to commits**

- tags / branches 
- head - pointer to the current commit  (very last commit we made)
- changing branches in git is fast - we change pointer 



```
.git
|____ HEAD
|____ refs
	 |____ heads
	 |	  |____ master
	 |____ tags
```

>refs/heads. -> where branches lives 

```bash
git log --oneline  # 79fe1e2 (HEAD -> master) Initial commit
```

```bash
cat .git/refs/heads/master # 79fe1e2f93c7f04c555eceba49271083b2961ac0
```

HEAD is usually a pointer to the current branch

```bash
cat .git/HEAD # ref: refs/heads/master
```

```bash
git config --global core.editor 'subl -n -w'
```



### 4. Git Areas and stashing

#### 4.1 Working area / staging area / repo

- staging area - cache or index 

**working area**

- files are not in staging area - not handled by git
- called **untracked files**

**staging area - index \ cache**

- what files are going to be part of next commit 
- how git knows what will change bertween current and next commit 

baseline staging area - copy of the latest commit 



**repo**

- contains all commits 
- files git knows about 



**moving files in & out of staging area**

```bash
git add <file>
git rm <file>
git mv <file> 
# add / remove / rename a file in the next commit 
```



**unstage files from staging area**

- not removing files
- we are replacing file with copy what is currently in repo 





### 4.2 Git Stash

- save un-committed work 

```bash
git stash
git stash list # list changes
git stash show stash@{0} # shown contents
git stash apply # apply the last stash
git stash apply stash@{0}
```

```bash
git stash --include-untracked # keep untracked files 
git stash save "WIP: making progress on foo" # name stashes for easy reference
git stash branch <optional branch name> # start new branch from a stash 
git checkout <stash name> -- <filename>
git stash pop # remove last stash and apply changes 
git stash drop #remove last stash
git stash drop stash@{n} # remove the nth stash
git stash clear # remove all stashes
```



**excercise**

```bash
git ls-files -s
# 100644 ce013625030ba8dba906f756967f9e9ca394464a 0	hello.txt
echo "Test of git stashing syste" >> hello.txt
git add -p
# diff --git a/hello.txt b/hello.txt
# index ce01362..754d041 100644
# --- a/hello.txt
# +++ b/hello.txt
# @@ -1 +1,2 @@
# hello
# +Test of stashing system
# (1/1) Stage this hunk [y,n,q,a,d,e,?]? ?

git ls-files -s
100644 754d041dd2a498ee0af7bf7008968ffa311fb49e 0	hello.txt
```

**unstage your change**: 

- we want to start staging process over but without losing the changes to our file

basically changes are present in working area but remove from staging area

```bash
 git reset hello.txt
```

Use `git status` to see that our modifications to `hello.txt` are still there, but they're no longer staged for commit.



**stash your changes**

```bash
git stash save "Temporary changes in hello file"
```

now working area is clean 

Major benefit: 

- we are now free to change branches, do other work, etc. without our changes getting in the way.

to get more information

```bash
 git stash show stash@{0}
 #apply changes:
  git stash apply stash@{0}
  git diff hello.txt
  # index ce01362..754d041 100644
	# --- a/hello.txt
	#+++ b/hello.txt
	#@@ -1 +1,2 @@
	# hello
	#+Test of stashing system
```



## 5. References, commits, branches

### 5.1 References

 3 types: 

- tags / branches / head 

**branch**

- Pointer to particual commit
- pointer of current branch changes as new commits are made

**head**

- how git knows that branch you are currently on
- know what next parent will be 
- pointer 
  - usually points at the name of current branch
  - can point to a commit too (**detached head** )

- moves : 
  - make a commit in currently active branch 
  - when we checkout a new branch



```bash
git checkout master
cat .git/HEAD
ref: refs/heads/master
git checkout feature
cat .git/HEAD
ref: refs/heads/feature
```



```bash
mkdir posts
echo 'this is my very post entry' > posts/welcome.html
echo 'Index file of blog' > index.html
git init 
git add. 
git commit -m 'Initial commit'
git checkout -b tech_posts

echo 'Python info' > posts/python.txt
git add posts/python.txt
git commit -m 'New blog post about python'

```



### 5.2 Tags & Annotated tags 

- lightweight tags are just a simple pointer to a commit 

When we create a tag with no arguments - captures the value in head 

```bash
git checkout master
git tag my-first-commit
```



**annotated tags**

```bash
git tag -a v1.0 -m "Version 1.0 of my blog"
git tag # my-first commit
git show v1.0
```

> tag v1.0
> Tagger: programators <programatorseu@gmail.com>
> Date:   Thu Aug 18 21:36:18 2022 +0200
>
> Version 1.0 of my bog'
>
> commit 32ad2904071c57587ac2efa8764582be3f8377ba (HEAD -> master, tag: v1.0)

**working with tags**

```bash
git tag # list all tags in repo
git show-ref --tags # list all tags and what commit they are pointing to
git tag --points-at <commit> # list all tags pointing at commit 
git show <tag-name> # looking at the tag or tagged contents 
```



```bash
	echo "Hash boject" | git hash-object --stdin -w 
# get hash and saved as object
# create folder with first 2 chars and file - total hash 
```



### 5.3 Detached Head & Dangling Commits

**head-less**

-> 1. sometimes we need to checkout to specific commit or tag / instead of branch 

-> 2. git move **HEAD** pointer to that commit we specified

-> 3. as soon as we checkout different branch or commit - **HEAD** will point to the new SHA

no commits in detached state 

 if we make any commits in a **DETACHED HEAD state**

to save them - create new branch that points to the last commit we made in a detached state

```bash
git branch <new-branch-name> <commit>
```





#### 5.4 Sum up

```bash
git clone
git checkout exercise3
# show where is head
cat .git/HEAD 
git branch
# show which commits our HEADS are pointing at 
git show-ref --heads
# see master : 
git cat-file -p 43388fee19744e
# 3 : lightweight tags : 
git tag my-excercise3-ta
git show-ref --tags
git tag -a "exercise3-annotated-tag" -m "This is my annotated tag for exercise 3"


```



**detached head**

- checkout to the latest commit : 

```bash
git log --oneline
git checkout e348ebc
cat .git/HEAD
# e348ebc1187cb3b4066b1e9432a614b464bf9d07
```

Even though our `HEAD` is now pointing at a specific commit - instead of a branch or tag - we can still make commits

```bash
create file
git add 
git commit
git checkout exercise3
```

>   8075f64 This is a dangling commit

But wait. Because our new commit - `8075f64` in this case - was made in a detached `HEAD` state, it doesn't have any references pointing to it. It's not part of a branch, and has no tags. This is called a Dangling Commit. You'll see  this warning if you try to switch branches:



We may create a new branch that points to this commit. Git does a  periodic garbage collection and will eventually delete any commits that  don't have a reference pointing to them.



## 6. Mergig and Rebasing

### 6.1. Merging and Fast Forward

Merge commits are commits  - have more parents (mostly 2 parents)

merge commits - marker when new feature was merged to master 



**fast forwaerding**

when there is clean path from our current branch to target branch

- there are no commits on the base branch that occured after feature branch was created ! 

- we add new commits at the top of master branch
- move pointer 
  * we can loose track of features we have been working on 

```bash
git checkout master
git merge feature
# Conflict (add / add) : Merge conflict in feature 
```



**git rerere** - reuse | recorded | resolution 

=> save how we are going to resolve a conflict 

### 6.2 Merge conflicts

when we merge  but files are diverged ( )

do not need to do the same resolution on every file on and on

**turn it on**

```bash
git config rerere.enabled true 
#after we save conflict / add file / commit => record resolution 
```



### 6.3 Sum up 

1. merge `excercise3` into `excercise4`

```bash
git checkout exercise4
git merge exercise3
# Updating 43388fe..e348ebc

$> git log --oneline
e348ebc Testing the emergency git-casting system
43388fe Initial commit
```

- nothihg let us know what e348ebc was merged into `exercise4` from the `exercise3` branch
  - it was fast-forward merge - no changes made to `exercise4`
  - the pointer for `exercise4` was simply updated via "fast-forward" to point to our new commit, `e348ebc`.\

**Fast forward** commits happen when the base branch (the one  you created your branch on) hasn't changed after you branched from it.  The history is linear.



2. non-fast-forward merge

   ```bash
   git reset --hard HEAD^ # back things up to the last commit
   #HEAD is now at 43388fe Initial commit
    git merge --no-ff exercise3 # editor open to type message 
   ```
   
   now we have a merge commit in place to tell us that `e348ebc` was merged into `exercise4`
   
   ```bash
   git log --graph
   ```
   
   > *   commit 7ea0b8043761bbb93594ca187afd3c8c7d1843e0 (HEAD -> exercise4)
   > |\  Merge: 43388fe e348ebc
   > | | Author: programators <programatorseu@gmail.com>
   > | | Date:   Tue Aug 30 22:27:55 2022 +0200
   > | |
   > | |
   
   
   
   3. setting up for a Conflict 
   
   git's Reuse Recorded Resolution 
   
   - create new branch  <mundo>
   - edit hello.txt
   - add
   - commit 
   - go back to excercise4 branch -> create commit that chnage hello 
   - enable rere 
   - Merge
   
   ```bash
    git config rerere.enabled true
     git merge mundo
   ```
   
   Run `git rerere diff` to see what's going on
   
   Resolve the conflict in `hello.txt` and run `rerere diff` again:
   
   > git will know when it will see 2 values on each side always choose one we pick up 
   
   



### 7. History and Diffs

### 7.1 Useful Commit messages

as project drags on  - messages are less and less informative 

good messages :

- enapsulate logic idea 

```
Add a README file to the project
```



### 7.2 Git log

```bash
git log --since="yesterday"
git log --since="2 weeks ago"

git log --name-status --follow -- <file>  # move or renamed file 
```

name-status => return what was name before file  was moved or renamed 

```bash
git log --grep=mail # search for commit messages that match a regular expression
```

we can mix & match with other flags

```git
git log --grep=mail --author=piotr --since=2.weeks
```



**git log diff-filter**

- include /exclude files that have been:
  - (A)dded , (D)eleted, (M)odified, (R)enamed

```bash
git log --diff-filer=R --stat
```

>tilda and hat 
>
>^ no args == ^1 -> first parent comit 
>
>^n ---> nth parent commit 



> ~ or ~n
>
> ~ no args == ~1 -> first commit back 
>
> ~n: number of commits back 



>Head > A
>
>​			|
>
>​			 B			C
>
>​			|			|	
>
>​		{D E F}		f 

a - very lates

D-F the oldest 

B-C  are parents of A (A is merged commit of those two )



with merge - > git track what commit was first and what second 

```
A = A^0
B = A^ = A^1 = A~1
C = A^2 
D = A^^ = A^1^1 = A~2
F =  A^2^1
```

### 7.3 Git Show and Diffs

```bash
git show <commit>  and contents 
git show <commit> --stat # show files changed in commit
git show <commit> : <file>

```

 

**diff**

- shows you changes:
  - between commits
  - between staing area and repo
  - what is in working area

unstaged changes - what could be in the next commit

staged changes - what will be in the next commit 

```bash
git branch --merged master # which branches are merged with master - can be cleaned up 
```



### 7.4 sum-up

```bash
git mv hello.txt hello.template
git add hello.template
git commit # type headline and body message 
```



```bash
git log --name-status --follow --oneline hello.template
```

>
>
>```
>4b2b90e Replacing greeting with tokens for i18n
>R073    hello.txt       hello.template
>fec9e7b Changing Hello to Hola
>M       hello.txt
>```





## 8. Fixing Mistakes

### 8.1 Git checkout

Working Area ---> Staging Area ---> Repo

​		*				 <---			*		<-- checkout

for retoring tree files or switch branches

* **when we git checkout** a branch

1) change HEAD to point to new branch
2) copy commit snapshot to the staging area
3) update working area with branch contents

-  **when we git checkout --file ? **

  - replace working area copy withe version from current staging area
  - replace file contents in working area with clean file contents  from repo
  - **overwrites files in working dir without warning**
  
  ```bash
  git checkout -- <file_path>
  ```

- **git checkout <commit> -- file**

  1. update staging area to match commit

  2. overwrite working area to match staging area 

     **very destructive command **

- git checkout from a specific commit

  - copies to both working area & staging area 

we can restore a deleted file 

```bash

git checkout <deleting_comit>^ -- <file_path>
```



### 8.2 Git clean & reset

- clean - clear working area by deleting untracked files 

  - can not be undone

  `--dry-run` to see what would be deleted

  `-f` to do deletion

  `-d` clean both files and dirs

```bash
git clean --dry-run
# would remove hello.txt
git clean -d --dry-run
# would remove /src and hello.txt
```



**git reset**

> performs different actions depending on arguments 

	- with path / without path

by default:  `git reset -mixed`

git checkout move HEAD but branch stays

git reset - move HEAD and brach - both are modified 

for commits :

- move HEAD pointer / optionally modifies files

 if we pass file path : does not move HEAD pointer  - just modifies files 



**soft**

```bash
git reet --soft HEAD~ # move pointer to previous commmit 
```

**mixed**

```bash
git reset --mixed HEAD~ # move HEAD and copies files to  staging area
```



**hard**

```bash
git reset --hard HEAD~ # move head to previous commit copies files to staging area copies to working area
```

1. Move HEAD and current branch
2. reset staging area
3. reset working area

```
--soft = (1)
--mixed = (1) & (2)
--hard = (1) & (2) & (3)
```

commits:

> (B) (Head is here)
>
> |
>
> |
>
> (A) (parent commit)

we run

`git reset --soft HEAD~`

`git commit -m "new commit"`

- we are pointing with HEAD to A commit
- making new commit - head is moved to new commit
- no reference to B 





`git rest -- <file>` 

- copies file from commit to staging area

**undo git reset**

in case of an accidental `git reset -`

 => git keepss previous value of HEAD in variable called `ORIG_HEAD`

=> go back with `git reset ORIG_HEAD`



### 8.3 Git REVERT

"safe" reset

- creates new commit that introduces opposite changes from specified commit
- original commit stays in repo

"*" use rever if you undoing a commit that has already been shared

"*" revet does not change history 

```bash
git revert 2b0b3f2 
```

### 8.4 Sum up

```bash
# make mistake in file
# mistake has not been comitted yet


$> echo "Bad data" > hello.template

$> cat hello.template
Bad data
git checkout  -- hello.template # we have checkout to the previous comitted version
```

By default, if you don't pass arguments to `git checkout`, it assumed you meant to use `HEAD`. **Note:** Remember that `git checkout --` is a destructive operation.



we want to checkout from specific point at time : 

```bash
git log --name-status --follow --oneline hello.template
```



>```
>4b2b90e Replacing greeting with tokens for i18n
>R073    hello.txt       hello.template
>fec9e7b Changing Hello to Hola
>M       hello.txt
>```

```bash
git checkout fec9e7b -- hello.txt
git status
```

> Changes to be committed:
>   (use "git restore --staged <file>..." to unstage)
> 	new file:   hello.txt

we do not want to have hello.txt

we want to remove it :

```bash
git reset HEAD hello.txt
git rm hello.template
git commit -m 'Delete hello.template'
```

Later on, we decide that deleting `hello.template` was an accident. Let's track down where we deleted it, and bring it back:

```bash
$> git log --diff-filter=D --oneline -- hello.template
713f6a1 Deleting hello.template

# Ah, it was deleted at 713f6a1. Let's use the caret (^) syntax to checkout hello.template from one commit before that

$> git checkout 713f6a1^ -- hello.template

$> git status
On branch exercise6
Your branch is ahead of 'origin/exercise6' by 1 commit.
  (use "git push" to publish your local commits)
...

$> cat hello.template
[greeting] [noun]!
This is a test of the emergency git-casting system.
```

**clean repo**

`dry-run` first to be safe

```bash
git clean --dry-run
git clean -f
```



**git reset**

we recomitted `hello.template` 

 By default, if you don't specify an argument to `git reset`, it'll assume you meant `HEAD`.

```bash
git reset -- hello.template # now file is untracked
git reset --hard HEAD
```

## 9. Rebase and AMend

- amend - quick and easy shortcut let make us changes to previous commit

```bash
git commit --amend
```

- we have different sha1 key - commits can not be edited ! .
- commit is referenced by SHA (date is different)



**rebase**

- we have 2 branches - that are diverged 
- we do not want a messy merge in our history



- pull in all the latest changes from master and apply our commits on top of them by changoing parent commit of our commits

rebase = give a commit new parent 

**example**

```bash
git checkout tech_posts
```

![image-20220909120528801](/home/programators/.var/app/io.typora.Typora/config/Typora/typora-user-images/image-20220909120528801.png)

```
git rebase master
```

![image-20220909120455253](/home/programators/.var/app/io.typora.Typora/config/Typora/typora-user-images/image-20220909120455253.png)

 HEAD is rewind back to `master`

put commit at the top 

![image-20220909120710361](/home/programators/.var/app/io.typora.Typora/config/Typora/typora-user-images/image-20220909120710361.png)

**we do not need to have conflicts in file between 4080a and 25b38**



merge vs rebase

 -> rebase allow us to keep history more clean - especially in open source projects 

**Interactive rebase** - `REBASE - I`

- opens an editor with a list of "todos"

  format:  `<command> <commit> <commit msg>`

  - git will pick commits in specified order or stop to take an action when editing or conflig occurs



```bash
git rebase -i <commit_to_fix>^ # ^ -> parent commit
```

> **pick** - keep commit
>
> **reword** - keep commit - just change msg
>
> **edit** - keep commit but stop to edit more than msg
>
> **squash** - combine this with previous one - stop to edit msg 
>
> **fixup** - combine commit with previous one -- keep prev commit msg 

my commits looks like 

```
08a4d36 (HEAD -> tech_posts) Dummy fake created
56f8dd3 (exercise5) Commit for rebase
```

```bash
git rebase -i 56f8dd3^
```

- reword and squash

```
f109f9d (HEAD -> tech_posts) Commit for rebase
5de0c8b Delete hello.template
547abfa Replace greetinh with token
```





**fixup and Autosquash**

```bash
git log
```



>22db926 (HEAD -> exercise5) Add a blog post about python
>56f8dd3 Commit for rebase
>5de0c8b Delete hello.template

- change posts.py

  add  and commit --fixup
  
  

```bash
git add posts.py
git commit -- fixup 22db926
git log --oneline
```

> f73c491 (HEAD -> exercise5) fixup! Add a blog post about python
> 22db926 Add a blog post about python
> 56f8dd3 Commit for rebase

```bash
git rebase -i --autosquash ^56f8dd3
```

#### 9.1 Abort

```bash
git rebase --abort
```



**before we do rebase / fixup** - make copy of current branh

```bash
git branch my_branch_backup # new branch without switching to it
# if we messed up : 
git reset my_branch_backup --hard 
```



- commit often ==> perfect later ==> publish once
- commit whenever we make changes 
- before pushing work to a shared repo :  rebase to clean up the commit history 



### 9.2 Review

```bash
$> echo "This is the first file" > first.txt

$> echo "This is the second file" > second.txt

$> git add first.txt
$> git commit -m "Committing two new files"
# as long as we have not pushed our commit to remote repo  - we can ammed last commit
$> git add second.txt

$> git commit --amend

```



**rebase**

```bash
# starting from master 
git checkout master
# create new feature branch 
git checkout -b exercise7-2
echo "New feature" > feature.txt
git add feature.txt
git commit -m "Adding a new feature"

# switch to master to make continue changes there
 git checkout master
echo "Master has continued to change" >> hello.txt
git commit -m "Master has continued to change"
```



Switching back to our feature branch. 

- It's a good idea to periodically merge in the `master` branch:
  -  to keep things up to date and minimize the number of conflicts when the feature branch is eventually merged into `master`.
  -   use rebase to replay our feature commits on top of `master`'s commits.

```bash
git checkout exercise7-2
git rebase master
```

wen working on a feature branch that's likely to conflict, I prefer to rebase from master often and fix conflicts 





**interactive rebase**

 we are still in feature brahnch

```bash
 echo "Another new feature" > another_feature.txt
 git add another_feature.txt
git commit -m "Adding another new feature"
```

we are done with feature 

- want to clean commits by combining them with `squash	`  and changing commit message with `reword`

```bash
git log --oneline
```

> **12040ee** (HEAD -> exercise7-2) Adding another new feature
> **182a2b0** Adding a new feature
> **1a74112** (master) Master has continued to change
> **43388fe** (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, list) Initial commit



Start interactive rebase

=  by passing in **one commit *before*** the one you want to start rebasing from

we are intersted in rebasing :

> **182a2b0** Adding a new feature



as re we can use : 

- `HEAD~2` - 2 commits before `HEAD`
- `182a2b0^` - points to one commit back - parent `ce8865e`
- `1a74112` - the actual commit before the one we want to rebase from
- `master` - since `master` points to `ce8865e`

```bash
git rebase -i HEAD~2
```

1st one to `reword`

2nd to `squash`

```bash
git log --oneline
```





> 2267a5a (HEAD -> exercise7-2) Adding 2 features
>1a74112 (master) Master has continued to change
>43388fe (origin/master, origin/exercise7, origin/exercise4, origin/exercise2, origin/HEAD, list) Initial commit
>(END)



## 10. Remotes 

### 10.1 Github vs Git

git - open source version control software

gitbub : 

- repository hosting
- browse code
- issues
- pull requests
- forks



### 10.2 Remote

- git repo stored elsewhere

- **origin** - default name git gives to the server we cloned from



cloning remote repo - fetch whole repo and make a local copy in our `.git` folder 

different priveleges for a remote 



Remote repo  --- pull --> Local repo

​						<--push <--						

```bash
git remote -v
```



### 10.3 Fork pull requests & upstreams

fork - copy of repo that is stored in your git account

​	- we can clone fork to your local computer 

- there are not restrictions : we can push changes / do whatever we need with that copy 



if want to merge changes to original project from a fork 

-> **pull request**

> ​	we fixed the bug - do you accept changes i made 



**upstream**

- we fork and other changes are getting merged into source repo  / in order to say up to date -**set up upstream**

- base repo we created fork from
- is not set up by default - we need do it manually 
- by adding an upstream remote - we can pull down changes that have been added to original repo 

```bash
git remote add upstream <url>
```



### 10.4 Triangular workflow

1. fetch from **upstream** to keep local repo up to date 

2. push changes to your fork - `origin`

3.  propose changes to **upstream** via `pull request`



**tracking branches** 

- tie it to an upstream branch

```bash
# checkout a remote branch with tracking
git checkout -t origin/feature
# tell git which branch to track first time we push
git push -u origin feature
```

**fetch**

 -> does not change our local repo !! 

-> get information from server without merging or pulling 



**pull**

- fetch & merge 
- pull down all changes from remote repo to local repo and merge them with local branch



**review**

1. fork of github
2. setup remotes 

```bash
git remote -v
#rename to upstream
git remote rename origin upstream
# add a new remote - our fork of the repo
$> git remote add origin https://github.com/programatorseu/advanced-git-exercises
git pull --rebase upstream master
```

## 11. Danger

- use `ORIG_HEAD` to undo merges

`git reset --merge ORIG_HEAD`



git keeps commits around for about 2 weeks .  after that time - look in reflog

```bash
git reflog
```

on github page if we type -> `?`  we got shortcuts



### 12.Continous Integration

- merge smaller commits freq. instead of doing 1 big merge
- features can be realesed quicker
- CI **works ony well when there are tests ** that ensure that new commits did not break the build 

travis-ci.org
