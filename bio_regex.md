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

This is a good time for a tour of https://regex101.com

### Character classes `[]`

The first part of regular expressions we'll look at are **character classes**. This will match one or more characters of a certain type.

We use square brackets `[]` to around a list of characters to define the class

The brackets are the first *metacharacters* we're seeing. Metacharacters have special meanings in regex as compared to literals which are the actual character that was entered.

Some examples:
```
[acgt]       Matches a single a, c, g or t character (note all possible characters are listed with no spaces, commas etc. between them)
[A-Z]        Matches any single capital letter (the hyphen - has special meaning here)
[A-Za-z]     Matches any single capital or lowercase letter (you can combine more than one range)
[A-Za-z0-9]  Matches any single capital or lowercase letter or digit
[A-Z-]       Matches any single capital letter or the hyphen character
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

`GA[AG]`
</details>

<details>
  <summary>Find sequences that are NOT Glu</summary>

`GA[^CT]`

Note that this finds the <code>-</code> as well as A and G.
</details>

<details>
  <summary>Find sequences that have any valid base in the third position</summary>

`GA[ACGT]`
</details>

<details>
  <summary>How would you modify the pattern to find any based in the third position to match lowercase or uppercase bases?</summary>

`[Gg][Aa][ACGTacgt]`

Or, invoke case insensitive in the "RegEx options"
</details>

#### Pre-defined character classes `\w`, `\d`, `\s` etc.

There are handy pre-defined character classes that can be helpful in searches:

```
\w        "word character": expands to [A-Za-z0-9_] (note: \w considers _ a word character here)
\d        "digit": expands to [0-9]
\W        NOT \w: [^A-Za-z0-9_]
\D        NOT \d: [^0-9]
\s        "whitespace" characters: space, tab, newlines etc.
\S        NOT whitespace
```

You can make character classes with pre-defined classes:

```
[\w$-]  Word characters (letters, numbers, underscores) with the addition of dollar signs and hyphens
[\d.-]  Digits, decimal points and hyphens
```

#### Wildcard: match any character `.`

A wildcard is like a character class that will match any character. The wildcard character is `.` and it does not need to be in brackets.

<details>
  <summary>TBD</summary>

<pre></pre>
</details>

#### Match a metacharacter as a literal (escaping) `\`

We've started seeing characters that are treated as special chracters in the regular expression. How would you match one of these characters if they occur in the text your matching?

You use a backslash `\` in your pattern to identify a character as being a literal match even if it's a metacharacter.

So, if you have the string `[See note below]` and you want to match the `[` you would use `\[` in your regular expression. regex101.com is very helpful in identifying where a character is being interpreted as a metacharacter.

Some of the common metacharacters that should be escaped: `[](){}\-.*?+|^$`

### Quantification (enumeration)

In our look into character classes above, we were matching only a *single* unknown character at a time. What if you want to match several characters to the same class?

Regular expression's quantification metacharacters allow you to specify the number of characters to match.

#### One or more [+]

This will match if there is at least one match of the character or character class.

Test string:
````
GAGGAA
GAGGAC
GAGGAT
GAGCAG
GAG---
````

<details>
  <summary>Find all lines with at least one nucleotide immediately following GAG</summary>

`GAG[GACT]+`
</details>

#### Zero or more [\*]

Like the `+` this will match any number of characters but it will also continue testing for a match if none are present.

Test string:
````
GAGGAAGAG
GAGGACGAG
GAGG-TGAG
GAG-AGGAG
GAG---GAG
````

<details>
  <summary>Find all lines that have nucleotides or any number of gap characters (hyphens) immediately after the GAG</summary>

`GAG-*[AGCT]+`

Note: This partially matches the third line because GAGG matches, we'll see in the next section
</details>

#### Zero or one match `?`

This will match either zero or exactly one of the characters.

<details>
  <summary>TBD</summary>

<pre></pre>
</details>


#### A specific range `{}`

You can specify a range of times match characters.

```
{x}: exactly x repeats
{x,}: x or more repeats
{x,y}: between x and y (inclusive) repeats
```

Test string:
```
GAGGAAGAGA
GAGGACGAG
GAGGTGAG
GAAGGAG
GAGGAG
```

<details>
  <summary>Create patterns to match these criteria: exactly 10 nucleotides, 8 or more nucleotides, 9 or 10 nucleotides</summary>

Exactly 10: `[ACGT]{10}`
8 or more: `[ACGT]{8,}`
9 or 10: `[ACGT]{9,10}`
</details>

<details>
  <summary>Match the parts of the lines where there are two or more G characters together</summary>

`G{2,}`

or: `GG+`

Note: we've been using numerators around character classes, but they can also be used for single characters
</details>

### Anchors/assertions `$`, `^`, `\b`

Sometimes you want to specify that a match occurs at a certain part of a line. You can use anchors or assertion characters to specify where in a line a match occurs.

The common anchors are:

```
^     Start of a line
$     End of a line
\b    Word boundary: a word character (\w) next to a word character \W
```

Test string:
```
GAGGAAGAG
GAGGACGAG
GAGG-TGAG
GAG-AGGAG
GAG---GAG
```

<details>
  <summary>Find lines that contain only nucleotides, no gaps</summary>

`^[ACGT]+$`

This would work to, although it might find invalid characters in the sequence:

`^[^-]$`

Note the two meanings of `^`. The first one is to anchor the beginning of the line and the second inside the brackets means not in a class.

This would work too, although it would also match invalid characters if they were in the sequence:

`^\w+$`
</details>

### Matching one pattern or another `|`

<details>
  <summary>TBD</summary>

<pre></pre>
</details>

## Working with sequence headers and replacing text

A common change to make with biological data is altering the headers (sequence names) in a fasta file. The header lines start with a `>` and are followed by lines with the sequence.

Test string:
```
>locus-1022_NMNH-2020-06-02
GAGGAAGAGGAGGATAGAGGAGGT--GAAGAGGAGGAAGAGGTCAGGAAGAGGAGGAAGAG
>locus-301_Sp.nov.ab
GAGGAAGAGGA-GATAGAGGAGGT---AAGAGGAGGAAGAGGT-AGGAAGAGGAGGAAGAG
```

In this exercise we're going to take these headers and alter them so that the Sample ID precedes the Locus ID.

In these headers the Locus and Sample IDs are separated by an underscore `_`. The locus ID always starts with `locus-`. The Sample ID follows the `_` (but does not contain underscores or spaces)

<details>
  <summary>Part 1: Write a pattern to match the Locus ID</summary>

`locus-\d+`
</details>

<details>
  <summary>Part 2: Write a pattern to match the underscore and Sample ID</summary>

`_.+$`
</details>

<details>
  <summary>Part 3: Combine the patterns in Parts 1 and 2 to match the two IDs, the underscore and the starting `>`</summary>

`^>locus-\d+_.+$`

We can optionally simplify this because now we have anchors of the beginning of the line, the underscore and the end of the line:

`^>.+_.+$`
</details>

### Replacing text: capture groups `()`

To continue, we need to learn about the special tools regular expressions provide for replacing text.

We've seen how regular expressions can match a variety of text rather than the fixed text of a traditional "find." What if we need to replace the text with a portion of what was found?

Regular expressions allow us to "capture" found text and use it in the replacment. We use parentheses `()` the text we want to save and reuse in the replacemnt.

Let's try it with our fasta header line pattern: `^>locus-\d+_.+$`

If we add parentheses around a portion of the pattern, say the Locus ID section: `^>(locus-\d+)_.+$` we see in regex101.com that the locus portion is in a different color and labeled "Group 1".

<details>
  <summary>Part 4: Write a pattern to match and separately capture using <code>()</code> the Locus ID and Sample ID</summary>

`^>(locus-\d+)_(.+)$`
</details>

#### Using the captured text `\1`, `\2` etc.

To reveal regex101.com's replacement features, choose "Substition" under the "Function" section in the left pane.

In the replacement text box, we can enter plain text and a reference to the captured text.

The first captured text is referenced with `\1`, the second with `\2` etc. You can have up to 9 captured groups, but there are ways to have more if needed (see "Named capturing groups" in the regex101.com reference section).

<details>
  <summary>Part 5: Using the pattern from Part 4, write the replacement string to have the Sample ID precede the Locus ID with an underscore separating the two. Make sure to add the <code>></code> at the start of the header line</summary>

Search for: `^>(locus-\d+)_(.+)$`

Replace with: `>\2_\1</pre>`
</details>
