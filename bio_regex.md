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
[acgt]        Matches a single a, c, g ,t character (note all possible chracters are listed with no spaces, commas etc. between them)
[A-Z]         Matches any single capital letter (the hyphen - has special meaning here)
[A-Za-z]      Matches any single capital or lowercase letter (you can combine more than one range)
[A-Za-z0-9]   Matches any single capital or lowercase letter or digit
[A-Za-z0-9-]  Matches any single captial or lowercase letter, digit or the hyphen character
                (If the final character is a - it won't be used as a range.
                This also shows combining several ranges with a single character)
```

Square brackets
	Ranges
	Nots
	Pre-defined classes

Try it: find codons for Glu (GAA, GAG)

Test string:
````
GAA
GAC
GAT
GAG
GA-
````

<details>
  <summary>Answer:</summary>
<pre>GA[AG]</pre>
</details>
