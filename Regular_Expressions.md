# Regular Expressions for Biologists

## About this training

### Objectives of this lesson

- Describe what regular expressions are.
- Examples of how regular expressions can be used with your work.
- Syntax to build regular expressions.
- Resources to learn more.

## What are Regular expressions?

- Regular expressions (or **regex**) is a way to make powerful search patterns. Unlike a standard 'find and replace' that only finds exact matches, you can use regexes to find a range of matches that fit the pattern you create.
- It's best to think of regular expressions like a programming language. We'll be learning the syntax and operations that to build your patterns.

With Regular Expressions, you can save the day!

![xkcd comic about regex](https://imgs.xkcd.com/comics/regular_expressions.png)

https://xkcd.com/208/

## How can you use regular expressions in your work?

- Extracting a certain type of annotation from a gff file
- Editing fasta file sequence headers
- Specifying sequence read names in a pipeline like Phyluce

## Applications that use regular expressions

*Regular expressions are implemented in many applications*

- Command line tools including `grep`, `sed`, `awk`
- Databases, e.g. `REGEXP` in `sqlite3`
- GUI applications: Geneious (batch rename)
- Programming/scripting: e.g. [Python](https://docs.python.org/3/library/re.html), [R (in tidyverse)](https://stringr.tidyverse.org/articles/regular-expressions.html), [BASH](https://tldp.org/LDP/abs/html/x17129.html)
  - Important note about wildcards for file selection on the command line: If you're familiar with using wildcards on the command line to select files (e.g. `ls *.tar.gz`), the format and syntax of these wildcards are not regular expressions, rather it's [globbing](https://en.wikipedia.org/wiki/Glob_(programming)) which has a different format and metacharacter usage. BASH does have support for regex, see [here](https://tldp.org/LDP/abs/html/x17129.html).

## Learning Regular expressions

- We will be using the website [regex101.com](https://regex101.com) to learn regular expressions
  - Allows you test regular expressions and shows what text matches
  - Gives visual indicators of how characters in your pattern are being interpreted
  - Has an integrated reference
- Regular expressions syntax varies with different implementations. We will be learning Perl style regex (or PCRE: Perl Compatible Regular Expressions)
  - Perl regex syntax is a very common implementation
  - For implementations that are different, Perl regex is similar enough that you can use what you learn from this style in other types of regular expressions

This is a good time for a tour of https://regex101.com

- Central pane
  - Regular expression: where you will enter your search pattern
  - Test string: text to search with the pattern
- Right pane
  - Explanation: details on the meaning of your pattern
  - Match information: Each match (Note: you can export matches from this pane)
  - Quick reference: Regex reference guide
- Left pane (click on three lines to reveal)
  - Flavor: style of regex patterns, we're using PCRE
  - Function
    - Match: Only match text, no replacement
    - Substition: Match and replacement option
  - Settings (wrench icon)
    - Max Execution Time: increase for very large strings
    - Editor: option for display of test string and entry of pattern

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

---

<details>
  <summary>Find sequences that are NOT Glu</summary>

`GA[^CT]`

Note that this finds the <code>-</code> as well as A and G.
</details>

---

<details>
  <summary>Find sequences that have any valid base in the third position</summary>

`GA[ACGT]`
</details>

---

<details>
  <summary>How would you modify the pattern to find any based in the third position to match lowercase or uppercase bases?</summary>

`[Gg][Aa][ACGTacgt]`

Or, invoke case insensitive in the "RegEx options"
</details>

---

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

---

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

#### Zero or more [\*]

Like the `+` this will match any number of characters but it will also continue testing for a match if none are present.

#### Zero or one match `?`

This will match either zero or exactly one of the characters.

#### A specific range `{}`

You can specify a range of times match characters.

```
{x}: exactly x repeats
{x,}: x or more repeats
{x,y}: between x and y (inclusive) repeats
```

#### Quantification excercises

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

---

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

---

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

---

<details>
  <summary>Match the parts of the lines where there are two or more G characters together</summary>

`G{2,}`

or: `GG+`

Note: we've been using numerators around character classes, but they can also be used for single characters
</details>

---

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

---

### Matching one pattern or another `|`

<details>
  <summary>TBD</summary>

<pre></pre>
</details>

---

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

---

<details>
  <summary>Part 2: Write a pattern to match the underscore and Sample ID</summary>

`_.+$`
</details>

---

<details>
  <summary>Part 3: Combine the patterns in Parts 1 and 2 to match the two IDs, the underscore and the starting `>`</summary>

`^>locus-\d+_.+$`

We can optionally simplify this because now we have anchors of the beginning of the line, the underscore and the end of the line:

`^>.+_.+$`
</details>

---

### Replacing text: capture groups `()`

To continue, we need to learn about the special tools regular expressions provide for replacing text.

We've seen how regular expressions can match a variety of text rather than the fixed text of a traditional "find." What if we need to replace the text with a portion of what was found?

Regular expressions allow us to "capture" found text and use it in the replacment. We use parentheses `()` the text we want to save and reuse in the replacement.

Let's try it with our fasta header line pattern: `^>locus-\d+_.+$`

If we add parentheses around a portion of the pattern, say the Locus ID section: `^>(locus-\d+)_.+$` we see in regex101.com that the locus portion is in a different color and labeled "Group 1".

<details>
  <summary>Part 4: Write a pattern to match and separately capture using <code>()</code> the Locus ID and Sample ID</summary>

`^>(locus-\d+)_(.+)$`
</details>

---

#### Using the captured text `\1`, `\2` etc.

To reveal regex101.com's replacement features, choose "Substition" under the "Function" section in the left pane.

In the replacement text box, we can enter plain text and a reference to the captured text.

The first captured text is referenced with `\1`, the second with `\2` etc. You can have up to 9 captured groups, but there are ways to have more if needed (see "Named capturing groups" in the regex101.com reference section).

<details>
  <summary>Part 5: Using the pattern from Part 4, write the replacement string to have the Sample ID precede the Locus ID with an underscore separating the two. Make sure to add the <code>></code> at the start of the header line</summary>

Search for: `^>(locus-\d+)_(.+)$`

Replace with: `>\2_\1</pre>`
</details>

## Extracting information from a `GFF` annotation file

A [GFF file](https://github.com/The-Sequence-Ontology/Specifications/blob/master/gff3.md) is used to annotate sequences. It is a tab-delimited text files with defined columns to describe to feature being annotated. Each row of the file is a different annotation. The last column (the 9th) is the `attributes` column, it contains additional information about the annotation that comes from the annotation pipeline that procuded the gff file.

In this portion of a [NCBI gff annotation file](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/300/575/GCF_000300575.1_Agabi_varbisH97_2/GCF_000300575.1_Agabi_varbisH97_2_genomic.gff.gz) for the [genome of the common mushroom *Agaricus bisporus*](https://www.ncbi.nlm.nih.gov/genome/?term=txid936046), the `attributes` column includes the NCBI IDs of the Genes that are annotated. These IDs are given as `Name=[Gene ID];`, for example, `Name=XP_006454979.1;` Note: not all annotations types have this ID.

**How can we extract the IDs given after `Name=`?**

Test string:

From [NCBI gff annotation file](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/300/575/GCF_000300575.1_Agabi_varbisH97_2/GCF_000300575.1_Agabi_varbisH97_2_genomic.gff.gz)

```
NW_006267344.1	RefSeq	CDS	4171	4284	.	-	0	ID=cds-XP_006454979.1;Parent=rna-XM_006454916.1;Dbxref=InterPro:IPR000330,InterPro:IPR001650,InterPro:IPR015194,InterPro:IPR015195,JGIDB:Agabi_varbisH97_2_175561,GeneID:18079446,Genbank:XP_006454979.1;Name=XP_006454979.1;gbkey=CDS;locus_tag=AGABI2DRAFT_175561;product=SNF2 family DNA-dependent ATPase;protein_id=XP_006454979.1
NW_006267344.1	RefSeq	gene	9630	11399	.	+	.	ID=gene-AGABI2DRAFT_113535;Dbxref=GeneID:18076336;Name=AGABI2DRAFT_113535;end_range=11399,.;gbkey=Gene;gene_biotype=protein_coding;locus_tag=AGABI2DRAFT_113535;partial=true;start_range=.,9630
NW_006267344.1	RefSeq	mRNA	9630	11399	.	+	.	ID=rna-XM_006453701.1;Parent=gene-AGABI2DRAFT_113535;Dbxref=GeneID:18076336,Genbank:XM_006453701.1;Name=XM_006453701.1;end_range=11399,.;gbkey=mRNA;locus_tag=AGABI2DRAFT_113535;partial=true;product=hypothetical protein;start_range=.,9630;transcript_id=XM_006453701.1
NW_006267344.1	RefSeq	exon	9630	11399	.	+	.	ID=exon-XM_006453701.1-1;Parent=rna-XM_006453701.1;Dbxref=GeneID:18076336,Genbank:XM_006453701.1;end_range=11399,.;gbkey=mRNA;locus_tag=AGABI2DRAFT_113535;partial=true;product=hypothetical protein;start_range=.,9630;transcript_id=XM_006453701.1
NW_006267344.1	RefSeq	CDS	9630	11399	.	+	0	ID=cds-XP_006453764.1;Parent=rna-XM_006453701.1;Dbxref=JGIDB:Agabi_varbisH97_2_113535,GeneID:18076336,Genbank:XP_006453764.1;Name=XP_006453764.1;gbkey=CDS;locus_tag=AGABI2DRAFT_113535;product=hypothetical protein;protein_id=XP_006453764.1
NW_006267344.1	RefSeq	gene	11528	11922	.	+	.	ID=gene-AGABI2DRAFT_62215;Dbxref=GeneID:18085933;Name=AGABI2DRAFT_62215;end_range=11922,.;gbkey=Gene;gene_biotype=protein_coding;locus_tag=AGABI2DRAFT_62215;partial=true;start_range=.,11528
NW_006267344.1	RefSeq	mRNA	11528	11922	.	+	.	ID=rna-XM_006453702.1;Parent=gene-AGABI2DRAFT_62215;Dbxref=GeneID:18085933,Genbank:XM_006453702.1;Name=XM_006453702.1;end_range=11922,.;gbkey=mRNA;locus_tag=AGABI2DRAFT_62215;partial=true;product=hypothetical protein;start_range=.,11528;transcript_id=XM_006453702.1
NW_006267344.1	RefSeq	exon	11528	11651	.	+	.	ID=exon-XM_006453702.1-1;Parent=rna-XM_006453702.1;Dbxref=GeneID:18085933,Genbank:XM_006453702.1;gbkey=mRNA;locus_tag=AGABI2DRAFT_62215;partial=true;product=hypothetical protein;start_range=.,11528;transcript_id=XM_006453702.1
```

<details>
  <summary>Part 1. Write a pattern to match <code>Name=</code> and all text until the next <code>;</code></summary>

`Name=[^;]+`

(This following works as well, but is somewhat less optimal because it requires fore knowledge of the characters used in the Gene IDs: `Name=[\w.]+;`)

</details>

---

<details>
  <summary>Part 2. Take the pattern from Part 1 and add a capture group <code>()</code> around the ID portion after <code>Name=</code></summary>

`Name=([^;]+)`
</details>

---

<details>
  <summary>Part 3. Add to your pattern to select the remainder of the line (left and right of <code>Name=</code>)</summary>

`^.*Name=([^;]+).*$`
</details>

---

<details>
  <summary>Part 4. Using the pattern from Part 3, write the replacement string to replace the lines with `Name=` with the captured ID</summary>

Search for: `^.*Name=([^;]+).*$`

Replace with: `\1`

Note: this procedure leaves lines that are missing `Name=` intact. It's possible to create a more sophisticated regular expression that removes those lines, but in practice it is easier to a filtering step after this with a command line program like `grep` to remove lines that are missing `Name=`.
</details>

---

*Optional:* The ID we extracted can be incorporated into a URL that opens the NCBI gene database page for the ID. The url is formatted: `https://www.ncbi.nlm.nih.gov/gene/?term=[Gene ID]`

<details>
  <summary>Part 5. Using the pattern from Part 4, re-write the replacement string so the Gene ID is used to create a NCBI gene database URL</summary>

Search for: `^.*Name=([^;]+).*$`

Replace with: `https://www.ncbi.nlm.nih.gov/gene/?term=\1`

Try out the URLs that were created.
</details>
