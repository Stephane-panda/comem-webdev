---

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [What is Git?](#what-is-git)
- [What is version control?](#what-is-version-control)
- [A short history](#a-short-history)
  - [**Local** version control systems](#local-version-control-systems)
  - [**Centralized** version control systems](#centralized-version-control-systems)
  - [**Distributed** version control systems](#distributed-version-control-systems)
- [Git basics](#git-basics)
  - [Snapshots, not differences](#snapshots-not-differences)
  - [Nearly every operation is local](#nearly-every-operation-is-local)
  - [Git has integrity](#git-has-integrity)
  - [What's in a Git project?](#whats-in-a-git-project)
  - [The three states](#the-three-states)
- [Getting started](#getting-started)
  - [Installing Git](#installing-git)
  - [First-time Git setup](#first-time-git-setup)
  - [Creating a new repository](#creating-a-new-repository)
  - [Checking the status of your files](#checking-the-status-of-your-files)
  - [Adding new files](#adding-new-files)
  - [Committing your changes](#committing-your-changes)
  - [Modifying files](#modifying-files)
  - [Removing files](#removing-files)
  - [Moving files](#moving-files)
  - [Making changes outside of Git](#making-changes-outside-of-git)
- [Viewing the commit history](#viewing-the-commit-history)
  - [Viewing the changes in the history](#viewing-the-changes-in-the-history)
  - [Viewing the list of changed files](#viewing-the-list-of-changed-files)
  - [Viewing the history with a custom format](#viewing-the-history-with-a-custom-format)
  - [Limiting log output](#limiting-log-output)
- [Undoing things](#undoing-things)
  - [Unmodifying a modified file](#unmodifying-a-modified-file)
  - [Unstaging a staged file](#unstaging-a-staged-file)
  - [Changing the commit message](#changing-the-commit-message)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->





## What is Git?

<a href='https://git-scm.com'><img src='images/git-logo.png' width='30%' /></a>

Git is a **version control system (VCS)** originally developed by Linus Torvalds, the creator of Linux.
Its goals are:

* **Speed**
* **Simple** design
* Strong support for **non-linear development** (thousands of parallel branches)
* Fully **distributed**
* Able to handle **large projects** like the Linux kernel efficiently (speed and data size)


<p class='center'><img src='images/commits.png' width='50%' /></p>




<!-- slide-column 40 -->

<img src='images/local-vcs.png' width='100%' />

<!-- slide-container -->

<img src='images/centralized-vcs.png' width='100%' />


* If any server dies, any of the client repositories can be copied back up to the server to **restore** it.
* You can **collaborate** with different groups of people in different ways simultaneously within the same project.
<img src='images/distributed-vcs.png' width='100%' />





## Git basics

<!-- slide-front-matter class: center, middle -->





### Snapshots, not differences

<!-- slide-column 50 -->

Other VCSs, like Subversion, store information as a list of file-based **changes**.

Git thinks of its data more like a set of **snapshots** of a miniature filesystem.

Every time you save the state of your project in Git, it basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot.
To be efficient, **if files have not changed, Git doesn't store the file again**, just a link to the previous identical file it has already stored.
Git thinks about its data more like a stream of snapshots.

<!-- slide-column 50 -->

**Changes**

<img src='images/deltas.png' width='100%' />

**Snapshots**

<img src='images/snapshots.png' width='100%' />





### Nearly every operation is local

Most operations in Git only need local files and resources to operate.
Because you have the entire history of the project right there on your local disk, most operations seem almost instantaneous:

* To browse the history of the project, Git simply reads it directly from your local database.
* To see the changes introduced in a file since a month ago, Git can look up the file a month ago and do a local difference calculation.
* If you're offline, you can commit happily until you get to a network connection to upload.





### Git has integrity

Everything in Git is check-summed before it is stored and is then referred to by that **checksum**.
This means it's impossible to change the contents of any file or directory without Git knowing about it.
This functionality is built into Git at the lowest levels and is integral to its philosophy.
You can’t lose information in transit or get file corruption without Git being able to detect it.

The mechanism that Git uses for this checksumming is called a [SHA-1][sha1] hash.
This is a 40-character string composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory structure in Git.
A SHA-1 hash looks something like this:

```
24b9da6552252987aa493b52f8696cd6d3b00373
```

You will see these hash values all over the place in Git because it uses them so much.
In fact, Git stores everything in its database **not by file name but by the hash value of its contents**.





### What's in a Git project?

The file structure in a Git project looks like this:

```txt
my-project:
  .git:
    HEAD
    config
    hooks
    index
    objects
    ...
  file1.txt
  file2.txt
  dir:
    file3.txt
```

A Git project has three main sections:

* The Git directory
* The working directory
* The staging area

#### The Git directory

The Git directory is where Git stores the metadata and object database for your project: all the **snapshots** of the different **versions** of your files.
This is the most important part of Git, and it is what is copied when you clone a repository from another computer.

It's located in the `.git` directory in the project's directory:

```txt
my-project:
* .git:
*   HEAD
*   config
*   hooks
*   index
*   objects
*   ...
  file1.txt
  file2.txt
  dir:
    file3.txt
```

There's no reason to modify any of the files in this directory yourself.
You could easily corrupt the Git repository.

#### The working directory (also called the working tree)

The working directory is a **single checkout of one version** of the project: these are **the files you are currently working on**.
These files are pulled out of the compressed database in the Git directory and placed in your project's directory for you to use or modify:

```txt
*my-project:
  .git:
    HEAD
    config
    hooks
    index
    objects
    ...
* file1.txt
* file2.txt
* dir:
*   file3.txt
```

#### The staging area (also called the index)

The staging area is a file, generally contained in your Git directory, that stores information about **what will go into your next commit**.

Before file snapshots are committed in the Git directory, they must go through the *staging area*.

```txt
my-project:
  .git:
    HEAD
    config
    hooks
*   index
    objects
    ...
  file1.txt
  file2.txt
  dir:
    file3.txt
```





### The three states

This is one of the **_most important things to remember about Git_**.

Git has three main states that your files can reside in: *committed*, *modified*, and *staged*:

* **Committed** means that the data is safely stored in your local database (the *Git directory*).
* **Modified** means that you have changed the file but have not committed it to your database yet.
* **Staged** means that you have marked a *new* or *modified* file in its current version to go into your next commit snapshot.
  It is in the *staging area*.

#### The basic Git workflow

The basic Git workflow goes something like this:

<p class='center'><img src='images/areas.png' width='60%' /></p>

* You **check out** a specific version of your files into the *working directory*.
* You **modify** files in your *working directory*.
* You **stage** the files, adding snapshots of them to your *staging area*.
* You do a **commit**, which takes the files as they are in the *staging area* and stores that snapshot permanently to your *Git directory*.





## Getting started

The rest of this documentation is a tutorial where you will learn how to:

* Configure Git for the first time
* Create a new repository
* Check the status of your files
* Track new files
* Stage and commit modified files
* Remove files
* Move files
* Ignore files





### Installing Git

There are a lot of different ways to use Git: the original command line tools and various GUIs of varying capabilities.
But the command line is the only place you can run **all** Git commands with all their options.

If you know how to run the command line version, you can probably also figure out how to run the GUI version, while the opposite is not necessarily true.
So the **command line** is what we will use.

Some of you may already have Git installed.
Run the following command in a terminal to make sure:

```bash
$> git --version
git version 2.11.0
```

If you don't have it, follow these [installation instructions][install-git] to install Git on your machine.





### First-time Git setup

Now that you have Git, you must configure your **identity**: your user name and e-mail address.
This is important because every Git commit uses this information, and it's *immutably* baked into every commit you make.

Use the `git config` command to do this:

```bash
$> git config --global user.name "John Doe"
$> git config --global user.email john.doe@example.com
```

You can also run the command with the `--list` option to check that the settings were successfully applied:

```bash
$> git config --list
user.name=John Doe
user.email=john.doe@example.com
```

Note that with the `--global` option, Git will store these settings in your user configuration file (`~/.gitconfig`),
so you only need to do this **once on any given computer**.
You can also change them at any time by running the commands again.





### Creating a new repository

Let's get started by creating a directory for our new project:

```bash
$> cd
$> mkdir hello-project
```

Go into the directory and run `git init` to create a Git repository:

```bash
$> cd hello-project
$> git init
Initialized empty Git repository in ~/hello-project
```

This creates a Git directory (`.git`) with an empty object database.
At this point, nothing in your project is tracked yet.





### Checking the status of your files

The main tool you use to determine which files are in which state is the `git status` command.
If you run it in the repo you just created, you should see something like this:

```bash
$> git status
On branch master

Initial commit

nothing to commit (create/copy files and use "git add" to track)
```

This means you have an empty repo with no commits, and a clean *working directory* – there is nothing there.

As you can see, Git often helps you by telling you what you can do next: you need to start adding some files.





### Adding new files

In the project's directory, write "Hello World" into a `hello.txt` file and "Hi Bob" into a `hi.txt` file:

```bash
$> echo "Hello World" > hello.txt
$> echo "Hi Bob" > hi.txt
```

Re-run the `git status` command:

```bash
$> git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

  hello.txt
  hi.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Those files are **untracked**.
Git will not include them unless you **explicitly** tell it to do so.

#### Tracking new files

In order to begin tracking a new file, you must use the `git add` command:

```bash
$> git add hello.txt
$> git add hi.txt
$> git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   hello.txt
    new file:   hi.txt
```

The files are now **staged**: they will be in the next commit.

**Tips:**

* `git add *.txt` would have added the two files in one command.
* `git add .` would have added all the files in the current directory (recursively).

#### Checking staged changes

Git can show you what you have **staged**:

```diff
$> git diff --staged
diff --git a/hello.txt b/hello.txt
new file mode 100644
index 0000000..557db03
--- /dev/null
+++ b/hello.txt
@@ -0,0 +1 @@
+Hello World
diff --git a/hi.txt b/hi.txt
new file mode 100644
index 0000000..e5db1d9
--- /dev/null
+++ b/hi.txt
@@ -0,0 +1 @@
+Hello Bob
```

It shows you each staged file and the changes in those files.





### Committing your changes

Now that your staging area is set up the way you want it, you can **commit** your changes:

```bash
$> git commit -m "Add hello and hi files"
[master (root-commit) c90aa36] Add hello and hi files
 2 files changed, 2 insertions(+)
 create mode 100644 hello.txt
 create mode 100644 hi.txt
```

Note that Git gives you the beginning of the new commit's SHA-1 checksum (`c90aa36`) along with change statistics and other information.

```bash
$> git status
On branch master
nothing to commit, working tree clean
```





### Modifying files

Let's make some changes to both files:

```bash
echo "You are beautiful" >> hello.txt
echo "Hi Jane" >> hi.txt
```

And see what Git tells us:

```bash
$> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   hello.txt
  modified:   hi.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

#### Staging modified files

Stage the changes on the `hello.txt` file and check the status:

```bash
$> git add hello.txt

$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  modified:   hello.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   hi.txt
```

If you commit now, only the changes on `hello.txt` will be included in the snapshot, while the changes in `hi.txt` will remain uncommitted.

#### Modifying a staged file

Before committing, let's make another change to `hello.txt` and check the status:

```bash
$> echo "I see trees of green" >> hello.txt

$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  modified:   hello.txt

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   hello.txt
  modified:   hi.txt
```

`hello.txt` is shown both under "Changes to be committed" and "Changes not staged for commit".
What does this mean?

#### Checking staged and unstaged changes

<!-- slide-column 40 -->

Use `git diff` with the `--staged` option to show **staged** changes.

You can also use it without the option to see **unstaged** changes.

<!-- slide-column 60 -->

```diff
$> git diff --staged
diff --git a/hello.txt b/hello.txt
index 557db03..2136a8e 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1 +1,2 @@
 Hello World
+You are beautiful
```

```diff
$> git diff
diff --git a/hello.txt b/hello.txt
index 2136a8e..730ea5a 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1,2 +1,3 @@
 Hello World
 You are beautiful
+I see trees of green
diff --git a/hi.txt b/hi.txt
index e5db1d9..f74a87a 100644
--- a/hi.txt
+++ b/hi.txt
@@ -1 +1,2 @@
 Hello Bob
+Hi Jane
```

#### Staging area versus working directory

This example shows you that the working directory and the staging area and really two separate steps.

* The version of `hello.txt` you have **staged** contains two lines of text ("Hello World" and "You are beautiful").
  This is what will be committed.

* The version of `hello.txt` in the **working directory** has an additional line of text ("I see trees of green") which you added later.
  It will not be included in the next commit unless you stage the file again.

<p class='center'><img src='images/areas.png' width='60%' /></p>

#### Committing partially staged changes

```bash
$> git commit -m "The world is beautiful"
[master b65ec9c] The world is beautiful
 1 file changed, 1 insertion(+)

$> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   hello.txt
  modified:   hi.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

As expected, the changes we did not stage are still **uncommitted**.
Let's fix that:

```bash
$> git add .

$> git commit -m "New lines in hello.txt and hi.txt"
[master dfc6c75] New lines in hello.txt and hi.txt
 2 files changed, 2 insertions(+)
```





### Removing files

You can remove files with Git:

```bash
$> git rm hi.txt
rm 'hi.txt'

$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  deleted:    hi.txt
```

If you remove the file yourself, you must still run the `git rm` command to remove it from the staging area.





### Moving files

You can move files with Git:

```bash
$> git mv hi.txt people.txt
$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  renamed:    hi.txt -> people.txt
```

Git actually doesn't store the fact that the file was moved:
it detects it after the fact.
This is equivalent:

```bash
mv hi.txt people.txt
git rm hi.txt
git add people.txt
```





### Making changes outside of Git

It's often simpler to just move or remove the files yourself:

```bash
$> mv hi.txt people.txt

$> git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  deleted:    hi.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

  people.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

#### Adding all changes

You can tell Git to add all changes (additions, modifications and removals):

```bash
$> git add --all .

$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  renamed:    hi.txt -> people.txt
```

Many developers simply modify and manipulate files in their favorite editor or IDE, then use the command above.





## Viewing the commit history

Git has a very powerful `log` command:

```bash
$> git log
commit e753ceb86806b285aa105a846c7295e826439637
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:50:07 2017 +0100

    New lines in hello.txt and hi.txt

commit 4c56257f622c53f1ddeaf3d58b6729b01b35aedb
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:50:00 2017 +0100

    The world is beautiful

commit c90aa36ffca90aad44572c0cb319037c921eceb2
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:01:06 2017 +0100

    Add hello and hi files
```

Use `git help log` to learn about its many options.





### Viewing the changes in the history

The `--patch` option shows the difference introduced in each commit:

```diff
$> git log --patch
commit e753ceb86806b285aa105a846c7295e826439637
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:50:07 2017 +0100

    New lines in hello.txt and hi.txt

diff --git a/hello.txt b/hello.txt
index 2136a8e..730ea5a 100644
--- a/hello.txt
+++ b/hello.txt
@@ -1,2 +1,3 @@
 Hello World
 You are beautiful
+I see trees of green
diff --git a/hi.txt b/hi.txt
index e5db1d9..f74a87a 100644
--- a/hi.txt
+++ b/hi.txt
@@ -1 +1,2 @@
 Hello Bob
+Hi Jane
```





### Viewing the list of changed files

The `--stat` option lists the files that have changed and gives abbreviated statistics:

```bash
$> git log --stat
commit e753ceb86806b285aa105a846c7295e826439637
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:50:07 2017 +0100

    New lines in hello.txt and hi.txt

 hello.txt | 1 +
 hi.txt    | 1 +
 2 files changed, 2 insertions(+)

commit 4c56257f622c53f1ddeaf3d58b6729b01b35aedb
Author: John Doe <john.doe@example.com>
Date:   Mon Jan 23 11:50:00 2017 +0100

    The world is beautiful

 hello.txt | 1 +
 1 file changed, 1 insertion(+)
```





### Viewing the history with a custom format

The `-pretty` option allows you to define your own format:

```bash
$> git log --pretty=format:"%h by %an %ad: %s" --date=relative
e753ceb by John Doe 5 hours ago: New lines in hello.txt and hi.txt
4c56257 by John Doe 5 hours ago: The world is beautiful
c90aa36 by John Doe 6 hours ago: Add hello and hi files
```

Check out the [available options][git-log-pretty-formats].





### Limiting log output

You can also limit which commits are listed.
The time-limiting options, in particular, are very useful:

```bash
$> git log --since=2.weeks
```

Here are some other useful options:

Option              | Limit to
:-                  | :-
`-(n)`              | The last n commits.
`--since, --after`  | Commits made after the specified date.
`--until, --before` | Commits made before the specified date.
`--author`          | Commits whose author entry matches the specified string.
`--grep`            | Commits with a commit message containing the string.
`-S`                | Commits adding or removing code matching the string.





## Undoing things

There are several ways of undoing things with Git.
We'll review a few of the tools available.

*Be careful:* you can't always undo some of these undos.





### Unmodifying a modified file

Sometimes you make a change and you realize you don't need it anymore.
Git actually tells you what to do to discard that change:

```bash
$> echo "Hi Steve" >> hi.txt

$> git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  modified:   hi.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Simply use `git checkout` as instructed:

```bash
$> git checkout hi.txt

$> git status
On branch master
nothing to commit, working tree clean
```





### Unstaging a staged file

If you have staged a file and don't need the changes in it anymore, a simple `git checkout` is not enough.
Again, Git tells you what to do:

```bash
$> echo "Hi Steve" >> hi.txt

$> git add hi.txt

$> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  modified:   hi.txt
```

Use `git reset` as instructed:

```bash
$> git reset HEAD hi.txt
Unstaged changes after reset:
M       hi.txt
```

The changes will still be in the file, but you can now use `git checkout` to discard them.





### Changing the commit message

Oops, you've used the wrong commit message and want to change it?

```bash
$> git commit -m "Fix teh prblme"

$> git commit --amend -m "Fix the problem"
```

If you notice that you've forgotten to stage a file into the commit, you can also amend the commit to include it.
The following commands will only create one commit:

```bash
$> git commit -m "Fix the problem"
$> git add TheFix.java
$> git commit --amend -m "Fix the problem"
```

**Be careful:** this changes the commit and its SHA-1 hash.
You should not do this if you have already shared this commit with others.



[sha1]: https://en.wikipedia.org/wiki/SHA-1
[install-git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
[git-log-pretty-formats]: https://git-scm.com/docs/git-log#_pretty_formats