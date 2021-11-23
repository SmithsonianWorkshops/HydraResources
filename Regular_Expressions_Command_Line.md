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


## Choice of system to learn on

  - There are two common distributions of sed and grep: GNU (on Linux systems) and BSD (on Macs).
  - There are minor, but important, differences between them. We've decided to focus on the GNU versions because Linux systems are more common for working with bioinformatic datasets and the GNU versions are more powerful.
  - For this course we'll be using a web-based linux system created with mybinder.org.
  - We'll mention differences in this documentation and how to install GNU versions on you Mac

## `sed`

`sed` is...

### Using `sed` to replace text *without* regex

Before we look at using regular expressions with `sed`, let's get familiar with using the text replace function in `sed`.

We'll be using the `s` command (short for substitute) in `sed`. It's the "find and replace" command. `s` is by far the most common `sed` command used in biology, but there are others such as `d` to delete a specific line or `p` to print certain lines.

Format of the `s` command:

```
sed 's/find/replace/' input.txt
```

You can also use `sed` with a pipe `|` in your command to take output from one command as input into sed:

```
echo "What are you trying to find?" | sed 's/find/replace/'
What are you trying to replace?
```

#### Global change in a line `/g`
The `s` command in this form will only find/replace the first occurence of the search string in each line. You can have it replace every occurrence by adding `g` (for global) to the end of the command.

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
