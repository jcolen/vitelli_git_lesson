# Vitelli git lesson

These are the notes from a git tutorial given at a Vitelli group meeting. If you don't want to read the wall of text below, just know these five git commands

1. `git status`  shows you what changes have been made since the last commit (version shapshot)
2. `git add` sets what files will be included in the next commit
3. `git commit` stores a snapshot of all staged (added with `git add`) changes
4. `git push` sends changes on your local computer to an online repository, such as Github
5. `git pull` downloads changes from an online repository onto your local computer

### 0. Install git onto your system

In order to use git you first need to have it installed. This can be done via the command line on Linux

`sudo apt install git`

and MacOS

`sudo brew install git`

On Windows, you'll need to go through an installer (instructions are available [here](https://git-scm.com)). 

### 0.1 Command line basics

The remainder of this tutorial will use the command line. There exist graphical interfaces for git but they're not recommended for two reasons. First, one of the most common use cases for git is managing repositories on the compute cluster, which is most easily accessed by the command line. Second, the command line is faster and more flexible for git use in nearly all cases, once you get past the initial learning curve. If you're unfamiliar with using the command line, there are a few essential commands (see e.g. the first 7 "ESSENTIAL" commands [here](https://www.tjhsst.edu/~dhyatt/superap/unixcmd.html)). 

### 1. Creating a git repository

Open a terminal in a place where you want your git repository to be, such as "Documents" or "Desktop". From there, you can create a folder, enter it, and initialize it as a git repository using the following three commands

`> mkdir my_git_lesson`

`> cd my_git_lesson`

`> git init`

Congrats! You've just created a git repository in the folder "my_git_lesson". This means we can use git to track changes to any files in this folder.

### 2. Managing and committing files

A git repository tracks the status of files at various points called *commits*. When you *commit changes*, git stores a snapshot of the file which you can revert to at any time. To see how this works, we'll create an empty text file in our new repository. 

`> touch temp.txt`

While we've created the file on our computer, it hasn't been added to the git repository. We can change this by using the ***three most important git commands:*** `git status`, `git add`, and `git commit`. First, let's check the status of our repository.

`> git status`

The output should show that git has recognized that a new untracked file `temp.txt` has been added to the folder. Because the file is untracked, its changes will not appear in any commits. To begin tracking it, add it to the repository

`> git add temp.txt`

Executing `git status` again will show that the next commit will include the addition of a new file `temp.txt`. However, this file is currently empty, which would make for a boring commit. Add some text to the file using your text editor of choice (vim, nano, and emacs are all good command line text editors to try). Next, execute `git status` for a third time and observe that there are now two entries. The first shows the changes we added before - the creation of a new (empty) file called `temp.txt`. The second indicates that the file `temp.txt` has been modified since we last staged it for commit. This second change is *not* staged for commit, meaning that git will *not* keep a record of the text we added if we commit now. 

We can fix this by executing `git add temp.txt` again. Executing `git status` once again will show just a single entry for `temp.txt` which means the new file *and* the text added to that file will be stored in our next commit. 

It's now time to actually commit our changes. We can do this using the command

`> git commit -m "Initial commit"`

Now the file exists in our git repository! We can continue to edit this file and include those changes in future commits. To see this, open up the file `temp.txt` and add or remove some text. Check the git status again to see that git recognizes the file has been modified. Stage these changes and commit them in a second commit.

`> git add temp.txt`

`> git commit -m "Made changes to temp.txt"`

Now we have two snapshots of `temp.txt` in two different commits. We can view a list of commits (and their commit messages, given by the `-m` flag) made to our git repository using `git log`. We can also see a summary of changes at each commit by executing `git log -p temp.txt`. 

Suppose that we make changes to our code that we are unhappy with (make another change to `temp.txt`, but a bad one). Using git, we can revert the file to one of these snapshots very easily. If we just want to remove all changes to a file since the last commit, we can do

`> git checkout -- temp.txt`

This allows you to safely try changes to your programs without having to worry about breaking it beyond repair. Alternatively, if you want to revert a file to an earlier commit, you can use `git revert` with a commit hash (viewable in `git log`) to change the file to the state *before* that commit, i.e. all changes made in and after that commit are undone. For example, using a commit hash from my sample repository:

`> git revert 5c7c42087fa239f46f92aeb1ce3cd2723acb7058` 

This reversion is recorded as a new commit. This means that if we decide later that we liked the changes made in a reverted commit, we can "undo the undo" and recover the program from that point in time. 

So far, we've seen how to use git version control to track, commit, and revert changes made to a repository. A few things to emphasize or clarify before we continue:

- Commits *only* store changes which have been *added*. Changes which haven't been added using `git add` will not be tracked
- `git status` shows the difference between your "local copy" of a file and the current snapshot stored on the git repository
- You can automatically stage all changes for commit using `git add -A`

### 3. Branching and merging

Git branches allow you to track multiple distinct sets of changes to your files. This is useful in situations like when two or more people sharing a git repository. Each branch tracks a separate line of commits. We can see a list of existing branches in our git repository by executing

`git branch`

Right now, there is just a single `master` branch. We can create separate branches by using `git checkout -b <branch name>`, for example

`> git checkout -b alice`

`> git checkout -b bob`

If we execute `git branch` again, it will show a list of three branches. The current branch is indicated by an asterisk. We're currently in the `bob` branch. Let's have Bob add some new text to `temp.txt` , create a new file `bob.txt` , and commit his changes.

`> echo "Bob added this sentence" >> temp.txt`

`> echo "Bob's special file" >> bob.txt`

`> git add temp.txt bob.txt`

`> git commit -m "Bob made some changes"`

Bob's changes have been updated in his branch, but Alice's files are unchanged. We can see this by switching over to the `alice` branch using `git checkout` (we don't need `-b` since we're not creating a branch)

`> git checkout alice` 

Notice that Bob's special file `bob.txt` no longer appears. In addition, if you open `temp.txt` you'll see that the sentence Bob added is no longer there! 

Let's have Alice add some of her own text to `temp.txt` , create another special file for herself, `alice.txt`, and commit those changes.

`> echo "Alice added this sentence" >> temp.txt`

`> echo "Alice's special file" >> alice.txt`

`> git add temp.txt alice.txt`

`> git commit -m "Alice made some changes"`

Alice's changes have now been updated in her branch. If we switch back to Bob's branch again using `git checkout` we can see that what Alice has done has not affected Bob's files. His version of `temp.txt` includes only his changes and he doesn't see Alice's new file. 

Now suppose we want to incorporate both Alice's and Bob's changes to the `master` branch. First, navigate to the master branch 

`> git checkout master`

The first thing to notice is that neither Alice's nor Bob's changes appeared in `master`. We'll fix that by *merging* their branches into the main branch, starting with Alice

`> git merge alice`

All of Alice's changes now appear in the main branch. We can merge Bob in the same way

`> git merge bob`

This time, there's an issue: a *merge conflict*. This happened because Bob edited a version of the file `temp.txt` which did not have Alice's changes. When git tries to merge Bob's branch, it doesn't know which version to keep, so it throws a conflict which has to be manually resolved. We can open `temp.txt` in a text editor and see that git has given us both Alice's and Bob's added text with some additional metadata indicating which text came from which branch. To complete the merge, we just manually choose which changes to keep (and remove the metadata) and commit them.

`> git commit -m "Merged Bob's branch to master"`

Now our `master` branch includes the changes we chose to keep from both Alice's and Bob's branch. This type of workflow is incredibly useful whenever you have multiple people responsible for maintaining the same code. 

### 4. Github, pushing, and pulling

Everything up to now has assumed we are working with a git repository stored on a single computer. Tools like [Github](https://github.com) (and others), which store repositories online, allow code to be shared easily across multiple computers. I'm going to create a new public repository on Github named "vitelli_git_lesson". We can access this repository from any computer by navigating to a place we want to store it and executing 

`> git clone https://github.com/jcolen/vitelli_git_lesson.git`

This will create a new folder `vitelli_git_lesson` and copy all files stored in the online repository into that folder. The folder will also include all of the git version tracking information from the online repository, so we can access past commits, see different branches that have been uploaded, and make our own changes. 

Right now though, the new folder is empty. We haven't copied any files because we haven't connected our local and online repositories. To do this, we need to add the Github repo as a *remote repository* and *push* our commits to it. 

`> git remote add origin https://github.com/jcolen/vitelli_git_lesson.git`

`> git push origin master` 

If we refresh our repository page on Github.com, we can see that our files now appear. Notice that only the `master` branch shows up right now. We can push the other branches separately using e.g. `git push origin alice` or all at once using `git push --all origin`. To access these files from our other computer, we need to *pull* them. Navigate to the folder where you cloned the empty online repository and execute 

`git pull` 

The folder will now contain all of the files and changes that have been committed. We can view all commits made on the original computer with `git log` and see Alice and Bob's branches if they've chosen to push them. 

Storing repositories on Github is great for collaboration since it doesn't require that two or more people share the same computer. It's also incredibly useful when working with the compute cluster. If you keep your code on Github, you can develop and debug it on a personal computer and pull changes/run working code on the cluster. This is an alternative to waiting for a debug/interactive node which can be a lottery depending on the day. Public Github repositories are also a great (and semi-standard) way to share code developed as part of research projects. 

### "Best" practices

-  `git pull` **at the beginning of every new session**. This limits the risk of having to resolve giant merge conflicts after making several commits
- **Meaningful commit messages**. It's much easier to revert changes and undo mistakes when you can tell precisely which commit you need to return to.
- **Frequent focused commits**. Commits should be done frequently (not once a month) and each have a distinct purpose or feature. Reverting changes is more painful if you commit only big chunks at a time (e.g. once a month). Suppose you make a commit with Changes A and B, but later realize you only wanted change A. If you added both in a single commit, you'd have to revert both A and B and then redo A. If you added them in separate commits, you just have to revert the commit containing B.
  - Focused commits also make it easier to write meaningful commit messages
- **Commit finished working code**. Code should be able to run in all commits. If you're working in a collaboration, you never want to pull and discover that nothing runs correctly anymore. It also helps to always be able to revert to working code. 
- **Commit generating, not generated files**. If your code is compiled, include the source code and compile script (e.g. a Makefile) but not the compiled executable. For Python, don't include `.pyc` or `__pycache__` files that are automatically generated. Why?
  1. These files can be machine specific. An executable on one machine might not run as is on another for various reasons. 
  2. Repos are easier to read/manage when there are fewer files in them 
- **Use a**  `.gitignore` . A `.gitignore` file includes a list of files (or filename patterns) that will be ignored by version control. They won't be tracked, appear in `git status`, or be added by `git add -A`. 
  - A `.gitignore` lets you automatically exclude generated files 

### Additional tips and tricks

- If you're confused about any git command, you can pull up the manual by executing `git <command> --help`
- [StackOverflow](https://stackoverflow.com) and Google can clarify everything else (and can be faster than the manual pages too)
- Github offers unlimited private repositories for students (or people with a .edu email address)
- If a file has any uncommitted changes, you can get a summary of them using `git diff`
- Using full commit hashes in `git revert` is unwieldy. You can get abbreviations of these hashes using `git log --abbrev-commit`
- *Relative commits* are even easier. HEAD refers to the current snapshot (and current branch). HEAD~ or HEAD~1 shows the most recent (parent) commit. HEAD~2 shows the next most recent commit, and so on
- You can see changes at specific snapshots using e.g. `git show HEAD~1`. You can see changes to specific files using e.g. `git show HEAD~1 temp.txt` . You can see what a file looked like a at a specific snapshot using e.g. `git show HEAD~1:temp.txt`