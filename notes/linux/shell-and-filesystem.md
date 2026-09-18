# Shell and Filesystem Basics

## Where Am I?

Show the current working directory:

    pwd

List directory contents:

    ls

Long listing:

    ls -l

Show hidden files too:

    ls -la

## Moving Around

Change directory:

    cd PATH

Move to the parent directory:

    cd ..

Move to my home directory:

    cd ~

Paths can be:

    absolute → starts from /
    relative → interpreted from the current directory

Example:

    /home/cipher/it-career/curriculum

is an absolute path.

From the curriculum directory:

    notes/linux

is a relative path.

## Files and Directories

Create a directory:

    mkdir NAME

Create parent directories as needed:

    mkdir -p PATH

Move or rename:

    mv SOURCE DESTINATION

Copy a file:

    cp SOURCE DESTINATION

Display a text file:

    cat FILE

Search for files:

    find PATH ...

Example:

    find notes -type f

## File vs Directory

`cat` reads file contents.

It does not list the contents of a directory.

Example:

    cat linux

may produce:

    cat: linux: Is a directory

To inspect that directory instead:

    ls linux

## Useful Inspection Habit

Before changing files, establish where I am and what is present:

    pwd
    ls

After changing something, verify it:

    ls
    find . -maxdepth 3 -type f | sort

Mental model:

    inspect → change → verify

## Remember

- `pwd` shows where I am.
- `ls` shows directory contents.
- `cd` changes directory.
- `..` means the parent directory.
- `~` represents my home directory.
- Absolute paths begin at `/`.
- Relative paths depend on the current directory.
- `mkdir` creates directories.
- `mv` moves or renames files.
- `cp` copies files.
- `cat` displays file contents.
- `find` searches a directory tree.
- Read command errors: they are evidence, not noise.
