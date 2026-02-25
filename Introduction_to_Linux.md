# Introduction to Linux

## What is Linux?
Linux is an open-source operating system. It is widely used in bioinformatics and research because it is:

- Free to use
- Fast for large datasets
- Flexible for many tools
- Powerful for automation with the command line

---

## Contents

- [What is Linux?](#what-is-linux)
- [What is the command line?](#what-is-the-command-line)
- [Core commands to get started](#core-commands-to-get-started)
  - [Print text with `echo`](#print-text-with-echo)
  - [Show your current location with `pwd`](#show-your-current-location-with-pwd)
  - [Create a folder with `mkdir`](#create-a-folder-with-mkdir)
  - [List files and folders with `ls`](#list-files-and-folders-with-ls)
  - [Getting help for any command](#getting-help-for-any-command)
  - [Move between folders with `cd`](#move-between-folders-with-cd)
  - [Understanding file paths](#understanding-file-paths)
  - [Redirect output to a file (`>` and `>>`)](#redirect-output-to-a-file--and-)
  - [Rename or move files with `mv`](#rename-or-move-files-with-mv)
  - [Copy files with `cp`](#copy-files-with-cp)
  - [Remove files with `rm`](#remove-files-with-rm)
  - [Remove directories](#remove-directories)
  - [Create and edit text files with `nano`](#create-and-edit-text-files-with-nano)
- [Creating and running a script](#creating-and-running-a-script)
- [Conclusion](#conclusion)
  - [Where to get help](#where-to-get-help)

## What is the command line?
The command line (also called a terminal, shell, or prompt) lets you control your computer using text commands.

Most commands follow this pattern:

```bash
command option argument
```

Example:

```bash
ls -l /home
```

- `ls` = command
- `-l` = option (long format)
- `/home` = argument (target folder)

---

## Core commands to get started

### Print text with `echo`

Run in terminal:

```bash
echo Hello World!
```

This prints `Hello World!` to the screen.

---

### Show your current location with `pwd`

Run in terminal:

```bash
pwd
```

`pwd` means **print working directory**.

**Note that Linux is case-sensitive**. For example:
- `pwd` works
- `Pwd` does not

---

### Create a folder with `mkdir`

Run in terminal:

```bash
mkdir new_directory
```

Tips for names:
- Use letters, numbers, `_` or `-`
- Avoid spaces and special characters such  as `? * & ! $ # @`

---

### List files and folders with `ls`

Run in terminal:

```bash
ls
```

Long format view:

```bash
ls -l
```

The `ls -l` fields include:
1. **Permissions**: for example `-rw-r--r--` (who can read, write, execute)
2. **Links**: number of hard links to the file/folder
3. **Owner**: user who owns the file
4. **Group**: group associated with the file
5. **Size**: file size in bytes (or human-readable with `-h`)
6. **Date/Time**: last modification time
7. **Name**: file or directory name

Example line:

```text
-rw-r--r-- 1 user group 2048 Feb 24 10:20 notes.txt
```

In this example, `notes.txt` is a regular file, owned by `user`, size `2048` bytes, last changed on `Feb 24 10:20`.

Helpful extra:

```bash
ls -lh
```

`-h` makes file sizes easier to read (KB, MB, GB).

#### Getting help for any command

Most commands have a help option that lists all available options and how to use them. You can use either `-h` or `--help`:

```bash
ls --help
```

This works for almost every command you will use. For example:

```bash
pwd --help
cp --help
rm --help
```

This is a very useful habit when you are unsure what a command can do, or when you want to find a specific option.

---

### Move between folders with `cd`

Go into a folder:

```bash
cd new_directory
```

Note you can use **tab auto-completion** here. For example, you could type `new` and hit the **Tab** key to auto complete the rest of the directory name. Note this only works if the directory exists.

Linux also has some very useful path shortcuts that work with `cd` and other commands — these are explained in the next section.

---

### Understanding file paths

A **path** tells Linux where a file or folder is.

Linux organises all files in a single tree structure. At the very bottom of that tree is **root**, written as `/`. Everything else — your home folder, programs, data — sits somewhere above it in the tree.

```text
/   ← root (the very bottom of the tree)
├── home/
│   └── your_username/   ← your home directory (~)
│       └── new_directory/
│           └── notes.txt
├── usr/
└── etc/
```

There are two main types of path:

1. **Absolute path**: starts from root `/` and gives the full location regardless of where you are
   - Example: `/home/your_username/new_directory/notes.txt`
2. **Relative path**: starts from your current location
   - Example: `new_directory/notes.txt` (only works if you are already in `/home/your_username/`)

Useful path shortcuts:
- `.` = current directory
- `..` = parent directory (one level up)
- `~` = your home directory
- `-` = the previous directory you were in

These shortcuts work with `cd` and most other commands. Try these examples:

```bash
cd ..       # go up one level
cd ~        # go to home directory
cd -        # go back to previous directory
ls .        # list current directory
ls ..       # list parent directory
ls ~        # list home directory
```

---

### Redirect output to a file (`>` and `>>`)

Normally, a command prints its output to the screen. **Redirection** lets you send that output into a file instead, using the `>` character.

Think of `>` as an arrow pointing output into a file:

```bash
echo Hello World! > new_file.txt
```

This creates `new_file.txt` containing the text `Hello World!` — nothing is printed to the screen.

Check the file was created:

```bash
ls
```

Print file contents to the screen with `cat` (**c**onc**at**enate):

```bash
cat new_file.txt
```

**Overwriting with `>`**

Using `>` again on the same file replaces its contents entirely:

```bash
echo Hello again! > new_file.txt
cat new_file.txt
```

**Appending with `>>`**

Using `>>` adds new lines to the end of the file without removing what is already there:

```bash
echo Hello World! > new_file.txt
echo Hello Again! >> new_file.txt
echo This is another line! >> new_file.txt
cat new_file.txt
```

In summary:
- `>` = overwrite (or create) a file
- `>>` = append to a file

---

### Rename or move files with `mv`

`mv` (**m**o**v**e) has two uses:
- **Rename** a file by giving it a new name in the same folder
- **Move** a file to a different folder

Rename a file:

```bash
mv new_file.txt another_file.txt
ls
```

Move a file into a folder:

```bash
mv another_file.txt new_directory/another_file.txt
```

---

### Copy files with `cp`

`cp` (**c**o**p**y) creates a duplicate of a file. The original is kept and a new copy is made. It takes two arguments: the source file and the name for the copy.

```bash
cp another_file.txt another_copy.txt
ls
```

To copy a file into a folder:

```bash
cp another_file.txt new_directory/
```

---

### Remove files with `rm`

`rm` (**r**e**m**ove) permanently deletes a file.

```bash
rm another_file.txt
rm another_copy.txt
ls
```

⚠️ **Important**: Files removed with `rm` are gone immediately — there is no recycle bin or undo in Linux. Always double-check before removing anything important.

---

### Remove directories

First move out of the directory you want to remove:

```bash
cd ~
pwd
```

Try removing a directory using `rm`:

```bash
rm new_directory
```

You will get an error because `new_directory` is a directory.

Use recursive remove:

```bash
rm -r new_directory
```

Check command help:

```bash
rm --help
```

⚠️ Be very careful with recursive remove.

---

### Create and edit text files with `nano`

Create a new file:

```bash
nano rbcl.fasta
```

Paste this FASTA content into `nano`:

```text
>NC_000932.1:54958-56397 Arabidopsis thaliana chloroplast, complete genome
ATGTCACCACAAACAGAGACTAAAGCAAGTGTTGGGTTCAAAGCTGGTGTTAAAGAGTATAAATTGACTT
ACTATACTCCTGAATATGAAACCAAGGATACTGATATCTTGGCAGCATTCCGAGTAACTCCTCAACCTGG
AGTTCCACCTGAAGAAGCAGGGGCTGCGGTAGCTGCTGAATCTTCTACTGGTACATGGACAACTGTGTGG
ACCGATGGGCTTACCAGCCTTGATCGTTACAAAGGACGATGCTACCACATCGAGCCCGTTCCAGGAGAAG
AAACTCAATTTATTGCGTATGTAGCTTATCCCTTAGACCTTTTTGAAGAAGGTTCGGTTACTAACATGTT
TACCTCGATTGTGGGTAATGTATTTGGGTTCAAAGCCCTGGCTGCTCTACGTCTAGAGGATCTGCGAATC
CCTCCTGCTTATACTAAAACTTTCCAAGGACCACCTCATGGTATCCAAGTTGAAAGAGATAAATTGAACA
AGTATGGACGTCCCCTATTAGGATGTACTATTAAACCAAAATTGGGGTTATCCGCGAAAAACTATGGTAG
AGCAGTTTATGAATGTCTACGTGGTGGACTTGATTTTACCAAAGATGATGAGAATGTGAACTCCCAACCA
TTTATGCGTTGGAGAGACCGTTTCTTATTTTGTGCCGAAGCTATTTATAAATCACAGGCTGAAACAGGTG
AAATCAAAGGGCATTATTTGAATGCTACTGCGGGTACATGCGAAGAAATGATCAAAAGAGCTGTATTTGC
CAGAGAATTGGGAGTTCCTATCGTAATGCATGACTACTTAACAGGGGGATTCACCGCAAATACTAGTTTG
TCTCATTATTGCCGAGATAATGGCCTACTTCTTCACATCCACCGTGCAATGCACGCTGTTATTGATAGAC
AGAAGAATCATGGTATGCACTTCCGTGTACTAGCTAAAGCTTTACGTCTATCTGGTGGAGATCATATTCA
CGCGGGTACAGTAGTAGGTAAACTTGAAGGAGACAGGGAGTCAACTTTGGGCTTTGTTGATTTACTGCGC
GATGATTATGTTGAAAAAGATCGAAGCCGCGGTATCTTTTTCACTCAAGATTGGGTCTCACTACCTGGTG
TTCTGCCTGTGGCTTCAGGGGGTATTCACGTTTGGCATATGCCTGCTTTGACCGAGATCTTTGGAGATGA
TTCTGTACTACAATTCGGTGGAGGAACTTTAGGCCACCCTTGGGGAAATGCACCGGGTGCCGTAGCCAAC
CGAGTAGCTCTGGAAGCATGTGTACAAGCTCGTAATGAGGGACGTGATCTTGCAGTCGAGGGTAATGAAA
TTATCCGTGAAGCTTGCAAATGGAGTCCTGAACTAGCTGCTGCTTGTGAAGTATGGAAAGAGATCACATT
TAACTTCCCAACCATCGATAAATTAGATGGCCAAGAGTAG
```

Save and exit:
- `Ctrl + O` (write out/save)
- Press `Enter` to confirm filename
- `Ctrl + X` (exit)

Check file:

```bash
cat rbcl.fasta
```

Edit the header line:

```bash
nano rbcl.fasta
```

Change the first line to:

```text
>arabidopsis_thaliana_rbcl
```

Save and exit again, then check:

```bash
cat rbcl.fasta
```

Show first 5 lines of a file:

```bash
head -n 5 rbcl.fasta
```

Show last 5 lines of a file:

```bash
tail -n 5 rbcl.fasta
```

---

### Creating and running a script

A script is a text file that contains a list of commands. This helps you automate repeated tasks.

Open a new file called `hello.sh` using nano:

```bash
nano hello.sh
```

Type or paste the following into the editor:

```bash
#!/bin/bash
echo "Hello from a script"
pwd
```

The first line `#!/bin/bash` is called a **shebang**. It always starts with `#!` followed by the path to the program that should run the script. In this case `/bin/bash` tells Linux to use the bash shell to interpret the commands in the file. Without it, Linux may not know how to run the script correctly.

Save and exit:
- `Ctrl + O` then `Enter` to save
- `Ctrl + X` to exit

Run it in two common ways:

```bash
bash hello.sh
```

or make it executable first:

```bash
chmod +x hello.sh
./hello.sh
```

---

## Conclusion

Congratulations! You now know the essential Linux commands needed to navigate a file system, manage files, and run scripts. These skills are the foundation for everything you will do on a Linux system, including running bioinformatics pipelines.

### Where to get help

You will not remember every command or option — and that is completely normal. Here are some useful places to look things up:

**In the terminal:**

```bash
command --help       # quick summary of options
man command          # full manual page (press q to exit)
```

**Online:**

- [The Linux Command Line (free book)](https://linuxcommand.org/tlcl.php) — a great beginner resource
- [Stack Overflow](https://stackoverflow.com) — search for any command or error message
- [Biostars](https://www.biostars.org) — bioinformatics-specific questions and answers
- [SEQanswers](https://www.seqanswers.com) — community forum for sequencing and bioinformatics

When searching online, include the word `linux` or `bash` in your search along with what you are trying to do — for example: `bash how to list files by size`.

**AI assistants:**

AI chatbots such as [ChatGPT](https://chatgpt.com), [GitHub Copilot](https://github.com/features/copilot), and [Claude](https://claude.ai) are also excellent resources. You can paste a command or error message and ask for a plain-English explanation, or simply describe what you want to do and ask for the command. They are particularly useful for understanding unfamiliar code or debugging problems quickly.
