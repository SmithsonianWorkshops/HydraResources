# Regular Expressions for Biologists

Initial draft: 2020-09-21

## About this training

### Objectives of this lesson

- ...how regular expressions can be used with your work
- Learn syntax to build regular expressions
- ...resources to learn more

### Part one of two

This is one of two part series. In this part we are focusing on the syntax of regular expressions without regard to specific applications.
In the next part we will be using regular expressions in two common command line tools: `sed` and `grep`

## What are Regular expressions?

- Best to think of regular expressions as a language...
- We will be using the website https://regex101.com to learn regular expressions
  - Allows you test regular expressions show what text matches
  - Gives visual indicators of how characters in your pattern are being interpreted
  - Has an integrated reference
- Regular expressions syntax differs with different implementations. We will be learning Perl style regex (or PCRE: Perl Compatible Regular Expressions.)
  - Perl regex syntax is a very common implementations
  - For implementations that are different, Perl regex is similar enough that you can use what you learn from this style in other types of regular expressions.

## How can you use regular expressions in your work?

- Pulling information out of text files
- ...editing fasta file headers
- Is common for tools to have a regex search option this can include text editors and online search engines.

## Learning Regular expressions

*This is a good time for a tour of https://regex101.com

### Character classes `[]`

The first part of regular expressions we'll look at are **character classes**. This will match one or more characters of a certain type.

We use square brackets `[]` to around a list of characters to define the class

Some examples:
```
[acgt]       Matches a single a, c, g or t character (note all possible chracters are listed with no spaces, commas etc. between them)
[A-Z]        Matches any single capital letter (the hyphen - has special meaning here)
[A-Za-z]     Matches any single capital or lowercase letter (you can combine more than one range)
[A-Za-z0-9]  Matches any single capital or lowercase letter or digit
[A-Z-]       Matches any single captial letter or the hyphen character
              (If the final character is a -, it won't be used as a range.
              This also shows combining several ranges with a single character)
```

You can also use the caret symbol (`^`) to find everything *not* in a range

`^` examples:

```
[^acgt]     Any single character that is not an a, c, g or t character
              The ^ must be the first character inside the [
[^A-Z]      Any single character that is not a capital letter
```

Square brackets
	Ranges
	Nots
	Pre-defined classes

Try out some examples:


Test string:
````
GAA
GAC
GAT
GAG
GA-
````

<details>
  <summary>Find codons for Glu (GAA, GAG)</summary>
<pre>GA[AG]</pre>
</details>

<details>
  <summary>Find sequences that are NOT Glu</summary>
<pre>GA[^CT]</pre>
Note that this finds the <code>-</code> as well as A and G.
</details>

<details>
  <summary>Find sequences that have any valid base in the third position</summary>
<pre>GA[ACGT]</pre>
</details>

<details>
  <summary>How would you modify the pattern to find any based in the third position to match lowercase or uppercase bases?</summary>
<pre>[Gg][Aa][ACGTacgt]</pre>
Or, invoke case insensitive in the "RegEx options"
</details>

There are pre-defined character classes that can be helpful in searches:

```
\w        "word character": expands to [A-Za-z0-9_] (note: _ is considered a word character here)
\d        "digit": expands to [0-9]
\W        NOT \w: [^A-Za-z0-9_]
\D        NOT \d: [^0-9]
\s        "whitespace" characters: space, tab, newlines etc.
\S        NOT whitespace
```
