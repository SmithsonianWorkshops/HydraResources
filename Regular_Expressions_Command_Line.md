# Using regular expressions on the command line

## Outline of course:

- Purpose of course
  - Learn advanced text manipulation from the command line using regular expressions
  - Focused on use in bioinformatics and processing large/many files.
- What we expect you already know
  - Some familiarity with regex (e.g. you took [Part 1 of this course](https://github.com/SmithsonianWorkshops/HydraResources/blob/master/Regular_Expressions.md) or reviewed that material on your own)
  - Basic Unix command line
- What we're covering
  - **sed**: for automated editing of files
  - **grep**: for selecting lines from a file
  - (we're not covering awk, mostly because I don't use it much with regex)


## GNU `sed` and `grep`

  - There are two common distributions of sed and grep: GNU (default on Linux systems) and BSD (default on Macs).
  - There are minor, but important, differences between them. We've decided to focus on the GNU versions because Linux systems are more common for working with bioinformatic datasets and we find the GNU versions are more powerful.

## System setup
  - There are several ways to get access to GNU versions of `sed` and `grep`

### Linux
  - If you're using a Linux system GNU versions will be available by default.
  - If your workstation is not Linux based, you can log in to a remote Linux system such as the Hydra computing cluster or using a virtual machine like this mybinder.org VM that we've setup for other trainings and can be used here: [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/SmithsonianWorkshops/binders/intro-shell?urlpath=lab)

### Mac
  - Conda: If you have [conda installed](https://conda.io/projects/conda/en/latest/user-guide/install/index.html), you can use this command to install the gnu versions of `sed` and `grep`:

  ```
  conda install -c conda-forge -c bioconda sed grep
  ```

  - Homebrew: We prefer using Conda for managing packages on a Mac, but if you use [Homebrew](https://brew.sh/), use these commands:

  ```
  brew install gnu-sed
  brew install grep
  ```

### TODO: Windows
  - WSL
  - cygwin
  - Git for Windows?

## `sed`

`sed` (short for [Stream Editor](https://en.wikipedia.org/wiki/Sed)) is a program used for editing text files. It reaches back to the very early days of Unix systems. It excels at working with large text files and for re-running the same types of edits on many files. The "Stream" portion of its name is because it doesn't the entire file into memory, it reads from disk as the file is being processed. Because of this, it can work with very large text files (like we sometimes see in bioinformatcs). It can also be used in conjuction with other commands via pipes where the output of one commands becomes the input of another.

`sed` uses commands from its scripting language to edit the files. We'll only be using the `s` script step (short for substitute) to change text in a file which is the most common use of sed, but there are other commands like `d` to delete specific lines or `p` to print certain lines that are sometimes used.

### Using `sed` to replace text *without* regex

Before we look at using regular expressions with `sed`, let's get familiar with using the text replace function in `sed`.

As mentioned above, we'll be using sed's `s` command (short for substitute)

Format of the `s` command:

```
sed 's/<find>/<replace>/<options>'
```

`<find>`: text (or regex) to find in the input file.
`<replace>`: text to replace the found text with.
`<options>`: options for the substitution (`g` for global, `i` for case insensitive). More about this below.

We our going to use a sample FASTA file, `locus_1.fasta`, to try `sed`

```
$ cat locus_1.fasta
>locus_1-seq_1
ACCGTTATC
>locus_1-seq_2
ACCGTTCTC
>locus_1-seq_3
ACCGTTGTC
>locus_1-seq_4
ACCGTTTTC
>locus_1-seq_5
ACCGTT-TC
>locus_1-seq_6
ACCGTTNNN
```

We will change "seq" in the sequence name lines with "sample".

Hint: a good way to get started with the sed line is by entering the outline of the `s` command for sed `'s///'` and then adding the specifics.

```
$ sed 's/seq/sample/' locus_1.fasta
>locus_1-sample_1
ACCGTTATC
>locus_1-sample_2
ACCGTTCTC
>locus_1-sample_3
ACCGTTGTC
>locus_1-sample_4
ACCGTTTTC
>locus_1-sample_5
ACCGTT-TC
>locus_1-sample_6
ACCGTTNNN
```

The text of the file is displayed to the screen with the changes made.

Another common way to use `sed` is to remove the found text. This can be done by leaving the replace section blank.
For example, referring back to the original `locus_1.fasta`, if you want to remove `sample_1` from each header, you can use:

```
$ sed 's/locus_1-seq_//' locus_1.fasta
>1
ACCGTTATC
>2
ACCGTTCTC
>3
ACCGTTGTC
>4
ACCGTTTTC
>5
ACCGTT-TC
>6
ACCGTTNNN
```

You may be wonder about re-using our original file if we already changed `seq` to `sample`.
Run the cat command on `locus_1.fasta` to find out.

`sed` will not change the original file unless you tell it do.

### Redirecting output

In Unix systems you can redirect the output from the screen to a file using the `>` character.

This command will create a new file, `locus1_mod.fasta`, that has the modified text.

```
sed 's/seq/sample/' locus_1.fasta >locus1_mod.fasta
```

### Changing original file (with or without a back)

Automatically create a backup:
```
sed -i.bak 's/seq/sample/' locus_1.fasta
```

`locus_1.fasta`: modified files
`locus_1.fasta.bak`: original file


Change the original with no backup (not this format doesn't work on BSD sed on Macs):

```
sed -i 's/seq/sample/' locus_1.fasta
```

|Warning: this changes your original file without making a backup, be careful with this and have your own backup available |
|---|

In the above example, the input text is from the file `locus_1.fasta`. You can also use `sed` with a pipe `|`  to take output from one command as input into sed. Here's a non-biolgical exmaple, but we'll see it again after we learn `grep`.

```
$ echo "What are you trying to find?" | sed 's/find/replace/'
What are you trying to replace?
```

### Global change in a line `/g`
The `s` command in this form will only find/replace the first occurrence of the search string in each line. You can have it replace every occurrence by adding `g` (for global) to the end of the command.

Compare the difference on `seq_6` with this command:
```
$ sed 's/N/-/' locus_1.fasta
>sample_1
ACCGTTATC
>sample_2
ACCGTTCTC
>sample_3
ACCGTTGTC
>sample_4
ACCGTTTTC
>sample_5
ACCGTT-TC
>sample_6
ACCGTT-NN
```

and this one that has the `g` global option:
```
sed 's/N/-/g' locus_1.fasta
>sample_1
ACCGTTATC
>sample_2
ACCGTTCTC
>sample_3
ACCGTTGTC
>sample_4
ACCGTTTTC
>sample_5
ACCGTT-TC
>sample_6
ACCGTT---
```

### Case insensitive `/i`
You can make the find portion of the `s` command case insensitive by adding `i` to the end of the `s` command.

```
echo "I need to go to the Smithsonian" | sed 's/smithsonian/museum/i'
I need to go to the museum
```

You can also combine `i` and `g` with `/ig`

```
echo "The Times has the best crosswords at times." | sed 's/times/post/ig'
The post has the best crosswords at post.
```

### Regular expressions in `sed`

`sed` can use regular expressions in its substitute command.

|Use the option `-r` to enable "Extended Regular Expressions, `sed -r`."
|---|


#### character classes

`[[:digit:]]` instead of `\d`
`[[:alnum:]]` instead of `\w` (except that `\w` includes `_`)
...

### back references

`\1`

### searching/replacing `/`

- escape it with `\`: `\/`
- Use a different special character instead of `/` in the `s` step: `sed 's|||'`



## `grep`

`grep` is another commonly used program for working with text files. The most common use is to return every line of a text file that matches a certain string or regular expression. Other related uses can be to print every line that *doesn't* match a certain pattern and printing the file names which contain or are missing a match.

### Using `grep` *without* regex

Like we did with `sed`, it's useful to first learn how to use `grep` without regular expressions.

The format of a `grep` command is:

```
grep <optional arguments> "<text to find>" <inputfile>
```

`<optional arguments>`: command line arguments including `-E` (use extended regular expressions, recommended), `-i` (case insensitive), `-c` (count of matches), `-v` (inverse search: lines *without* a match), `--files-with-matches`, `--files-without-match`
`<text to find>`: string or regular expression to find
`<inputfile>`: file to search, or grep can be used with `|` to pipe output from another command

### `grep` on multiple files

### Using regular expressions
