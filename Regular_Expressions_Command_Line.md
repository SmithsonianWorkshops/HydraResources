# Using regular experssions on the command line

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
  - If your workstation is not Linux based, you can log in to a remote Linux system such as the Hydra computing cluster or using a virtual machine like (mybinder) (TODO: add badge)

### Mac
  - Conda or Homebrew

### Windows
  - WSL
  - cygwin
  - Git for Windows?

## `sed`

`sed` (short for [Stream Editor](https://en.wikipedia.org/wiki/Sed)) is a program used for editing text files. It reaches back to the very early days of Unix systems. It excels at working with large text files and for re-running the same types of edits on many files. The "Stream" portion of its name is because it doesn't the entire file into memory, it reads from disk as the file is being processed. Because of this, it can work with very large text files (like we sometimes see in bioinformatcs). `sed` uses commands from its scripting language to edit the files. We'll only be using the `s` script step (short for substitute command to change text in a file), but there are other commands like `d` to delete specific lines or `p` to print certain lines.

### Using `sed` to replace text *without* regex

Before we look at using regular expressions with `sed`, let's get familiar with using the text replace function in `sed`.

As mentioned above, we'll be using sed's `s` command (short for substitute)

Format of the `s` command:

```
sed 's/<text to find>/<new text>/<options>' input.txt
```

`<text to find>`: text (or regex) to find in the input file.
`<new text>`: text to replace the found text with.
`<options>`: options for the substitution (`g` for global, `i` for case insensitive). More about this below.


TODO: example of using a file

In the above example, the input text is from the file `input.txt`. You can also use `sed` with a pipe `|`  to take output from one command as input into sed:

```
echo "What are you trying to find?" | sed 's/find/replace/'
What are you trying to replace?
```

### Working with multiple files

`sed -i ...`

#### Global change in a line `/g`
The `s` command in this form will only find/replace the first occurrence of the search string in each line. You can have it replace every occurrence by adding `g` (for global) to the end of the command.

```
echo "I need to go to the library" | sed 's/to/2/g'
I need 2 go 2 the library
```

#### Case insensitive `/i`
You can make the find portion of the `s` command case insensitive by adding `i` to the end of the `s` command.

```
echo "I need to go to the Smithsonian" | sed 's/smithsonian/museum/g'
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
`[[:...:]]` instead of `\w`
...

### back references

`\1`

### searching/replacing `/`

- escape it with `\`: `\/`
- Use a different special character instead of `/` in the `s` step: `sed 's|||'`

## `grep`
