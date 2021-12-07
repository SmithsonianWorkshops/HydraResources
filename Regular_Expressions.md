# Regular Expressions for Biologists

## About this training

### Objectives of this lesson

- Learn what are regular expressions.
- See examples of how regular expressions can be used with your work.
- Learn the syntax to build regular expressions.
- See resources to learn more.

## What are Regular expressions?

- Regular expressions (or **regex** or **regexp**) is a way to make powerful search patterns. Unlike a standard 'find and replace' that only finds exact matches, you can use regexes to find a range of matches that fit the *pattern* you create.
- We'll be talking about creating search patterns, these are search strings (text) that use characters with special meanings (metacharacters) to do this special type of searching.
- Is it a programming language? Not really, it doesn't have the full suite of operations a programming language has, but it does have strict formatting rules like you may find in a full language.

With Regular Expressions, you can save the day!

![xkcd comic about regex](https://imgs.xkcd.com/comics/regular_expressions.png)

https://xkcd.com/208/

## How can you use regular expressions in your work?

- Extracting a certain type of annotation from a gff file
- Editing FASTA file sequence headers
- Specifying sequence read names in a pipeline like Phyluce

## Applications that use regular expressions

*Regular expressions are implemented in many applications*

- Command line tools including `grep`, `sed`, `awk`
- Databases, e.g. `REGEXP` in `sqlite3`
- GUI applications: [Geneious](https://www.geneious.com/prime/) (batch rename), [NameChanger](https://mrrsoftware.com/namechanger/)
- Text editors: [Notepad++](https://notepad-plus-plus.org/), [Atom](https://atom.io/), [BBEdit](https://www.barebones.com/products/bbedit/), Sublime Text etc.
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
- Left pane (click on three lines to reveal if it's hidden)
  - Flavor: style of regex patterns, **we're using `PCRE`**, the default is `PCRE2`
  - Function
    - Match: Only match text, no replacement
    - Substitution: Match and replacement option
  - Settings (gear icon)
    - Max Execution Time: increase for very large strings
    - Editor: option for display of test string and entry of pattern


|In the `FLAVOR` section of regex101.com, choose "PCRE (PHP<7.3)" <br/> ![choose PCRE (PHP <7.3)](images/PCRE.png)|
|---|

### Search strings *without* regular expressions

Searching for text strings is a common activity on computers. A typical `Find` command in a program is only going to find exact matches for the search string:


Test string:

```
>seq_1
ACCGTTATC
>seq_2
ACCGTTCTC
>seq_3
ACCGTTGTC
>seq_4
ACCGTTTTC
>seq_5
ACCGTT-TC
```

Regular expression (all literal characters):
```
ACCGTT
```

The power of regular expressions is creating patterns that will match a range of text.

In the sections below we will be learning those patterns.

### Wildcard: match any character `.`

In our test FASTA file, the first six characters in each sequence are the same, but the seventh is different. How can you match that seventh character when you don't know what it is?

The `.` character has special meaning in regular expressions, it will match *any character*.

Regular expression:
```
ACCGTT.TC
```

It matches all five sequences of the FASTA.

The `.` is the first *metacharacter* we're seeing, rather than meaning match a period character it's special meaning is match any character.

### Match a metacharacter as a literal (escaping) `\`

You might be wondering if `.` means any character, how do a match an actual period character?

You use a backslash `\` in your pattern to identify a character as being a literal match even if it's a metacharacter.

The need for a `\` can depend on the context, sometimes a `.` is just a `.` (for example in a character class that we'll see in a moment), but it's not a problem to escape the character even it's already being treated as a literal.

some of the common metacharacters that should be escaped: `[](){}\-.*?+|^$`

So, to match a `\` you use `\\` :smile:


### Character classes `[]`

Sometimes we don't want to match any character, but a set of characters. These sets are **character classes**.

Character classes are defined in square brackets `[]` and list the possible matching characters.

Taking the example FASTA file from above, if we only want to match valid bases, we could use this:

```
ACCGTT[ACGT]TC
```

One thing different about this pattern and when we used the `.` character is the there are six characters `[ACGT]`, representing a pattern for one position.

Some examples other ways to :
```
[ACGT]       Matches a single A, C, G or T character (note, don't put no spaces, commas etc. between character in the class)
[A-Z]        Matches any single capital letter (the hyphen - means range of characters here)
[A-Za-z]     Matches any single capital or lowercase letter (you can combine more than one range)
[A-Za-z0-9]  Matches any single capital or lowercase letter or digit
[A-Z-]       Matches any single capital letter or the hyphen character
              (If the final character is a -, it won't be used as a range, instead it's a literal.
              You could also use \-, but it's not necessary.)

Note: regular expressions are case sensitive unless you tell them not to be as we'll see below.
```

You can use the caret symbol (`^`) to find everything *not* in a range. (Note: later in the lesson we'll see another use of `^` outside of a character class to denote the start of a line).

`^` examples:

```
[^acgt]     Any single character that is not an a, c, g or t character
              The ^ must be the first character inside the [
[^A-Z]      Any single character that is not a capital letter
```

An example of the `^` metacharacter in a character class using our sample FASTA file:

```
ACCGTT[^ACGT]TC
```

#### Exercises: character classes

Test string for this set of exercises *(copy into Regex101.com)*:
````
GAA
GAC
GAT
GAG
GA-
````

<details>
  <summary>Find codons for Glu (GAA, GAG in the <a href="https://www.ncbi.nlm.nih.gov/Taxonomy/Utils/wprintgc.cgi?chapter=tgencodes#SG1">standard genetic code</a>)</summary>

`GA[AG]`
</details>

---

<details>
  <summary>Find sequences with GA, but then have anything other than an A or G</summary>

`GA[^AG]`
<br><br>
Note that this finds the <code>-</code> as well as A and G.
</details>

---

<details>
  <summary>Find sequences that have any valid base in the third position</summary>

`GA[ACGT]`
</details>

---

<details>
  <summary>How would you modify the pattern to find lowercase or uppercase bases in the third position?</summary>

`GA[ACGTacgt]`
<br><br>
Or, just invoke case insensitive in the "RegEx options"
</details>

---

### Quantification (enumeration)

In our look into character classes above, we were matching only a *single* unknown character at a time. What if you want to match several characters to the same class?

Regular expression's quantification metacharacters allow you to specify the number of characters to match.

#### One or more `+`

This will match if there is at least one match of the character or character class.

#### Zero or more `*`

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

For some examples of this, let's look at another sample FASTA file:

```
>seq_6
ACCGTTATC
>seq_7
ACCGTTCGC
>seq_8
ACCGTTGCC
>seq_9
ACCGTTTAC
>seq_10
ACCGTT-TC
>seq_11
ACCGTTT-C
>seq_12
ACCGTT--C
```

Match any bases after `ACCGTT`:

```
ACCGTT[ACGT]+
```

Match next *two* bases after `ACCGTT`:

```
ACCGTT[ACGT]{2}
```

Match all hyphens, if present, (0 or more) immediately after `ACCGTT` and then all bases:

```
ACCGTT-*[ACGT]+
```

Match 0 or 1 hyphen immediately after `ACCGTT` and then all bases:

```
ACCGTT-?[ACGT]+
```


#### Exercises: Quantification

Test string *(copy into Regex101.com)*:
````
GAGGAA
GAGGAC
GAGGAT
GAGCAG
GAG---
````

<details>
  <summary>Find all lines with one or more nucleotides immediately following GAG (that is, not a gap `-`)</summary>

`GAG[GACT]+`
<br><br>
This matches lines 1-5, but not 6.
</details>

---

Test string *(copy into Regex101.com)*:
```
GAGGGAGAGA
GAGGACGAG
GAGGTGAG
GAAGGGG
GAGGAG
```

<details>
  <summary>Create patterns to match these criteria: exactly 10 nucleotides, 8 or more nucleotides, 9 or 10 nucleotides</summary>

Exactly 10 (line 1): `[ACGT]{10}`
<br><br>
8 or more (lines 1-3): `[ACGT]{8,}`
<br><br>
9 or 10 (lines 1, 2): `[ACGT]{9,10}`
</details>

---

<details>
  <summary>Match <i>the portions</i> of the lines where there are two or more G characters together</summary>

`G{2,}`
<br><br>
or: `GG+`
<br><br>
Note: we've been using numerators around character classes, but they can also be used for single characters
</details>

---

### Pre-defined character classes `\w`, `\d`, `\s` etc.

Many implementations of regular expressions have handy pre-defined character classes that can be helpful in searches:

These are the one used in `PCRE` (Perl Compatible Regular Expressions):

```
\w        "word character": expands to [A-Za-z0-9_] (note: \w considers _ a word character here)
\d        "digit": expands to [0-9]
\W        NOT \w same as [^A-Za-z0-9_]
\D        NOT \d same as [^0-9]
\s        "whitespace" characters: space, tab, newlines etc.
\S        NOT whitespace
```

You can include pre-defined classes in other character classes:

```
[\w$-]  Word characters (letters, numbers, underscores) with the addition of dollar signs and hyphens, note that because the hypen is at the end of the class, it's not considered a range.
[\d.-]  Digits, decimal points and hyphens
```


### Exercises: Quantification with pre-defined character classes
For these exercises we'll be a [GFF annotation file](https://github.com/The-Sequence-Ontology/Specifications/blob/master/gff3.md). These files list features of the sequence. The files have nine columns, the ninth column has additional information from the annotation pipeline that does not fit in the columns in the GFF file. These are often contain structured text that we can pull information out of. The one below is an excerpt from this [gff file](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/300/575/GCF_000300575.1_Agabi_varbisH97_2/GCF_000300575.1_Agabi_varbisH97_2_genomic.gff.gz) from a [fungal genome](https://www.ncbi.nlm.nih.gov/genome/858?genome_assembly_id=31528) annotated by NCBI's pipeline.

Test string *(copy into Regex101.com)*:
```
NW_006267344.1	RefSeq	CDS	3326	3553	.	-	0	ID=cds-XP_006453763.1;Parent=rna-XM_006453700.1;Dbxref=JGIDB:Agabi_varbisH97_2_189137,GeneID:18081018,Genbank:XP_006453763.1;Name=XP_006453763.1;gbkey=CDS;locus_tag=AGABI2DRAFT_189137;product=hypothetical protein;protein_id=XP_006453763.1
NW_006267344.1	RefSeq	CDS	8427	8432	.	-	0	ID=cds-XP_006454979.1;Parent=rna-XM_006454916.1;Dbxref=InterPro:IPR000330,InterPro:IPR001650,InterPro:IPR015194,InterPro:IPR015195,JGIDB:Agabi_varbisH97_2_175561,GeneID:18079446,Genbank:XP_006454979.1;Name=XP_006454979.1;gbkey=CDS;locus_tag=AGABI2DRAFT_175561;product=SNF2 family DNA-dependent ATPase;protein_id=XP_006454979.1
NW_006267344.1	RefSeq	CDS	9630	11399	.	+	0	ID=cds-XP_006453764.1;Parent=rna-XM_006453701.1;Dbxref=JGIDB:Agabi_varbisH97_2_113535,GeneID:18076336,Genbank:XP_006453764.1;Name=XP_006453764.1;gbkey=CDS;locus_tag=AGABI2DRAFT_113535;product=hypothetical protein;protein_id=XP_006453764.1
NW_006267344.1	RefSeq	CDS	11528	11651	.	+	0	ID=cds-XP_006453765.1;Parent=rna-XM_006453702.1;Dbxref=JGIDB:Agabi_varbisH97_2_62215,GeneID:18085933,Genbank:XP_006453765.1;Name=XP_006453765.1;Note=similar to hypothetical protein SNOG_03478;gbkey=CDS;locus_tag=AGABI2DRAFT_62215;product=hypothetical protein;protein_id=XP_006453765.1
NW_006267344.1	RefSeq	CDS	13993	14057	.	+	2	ID=cds-XP_006453766.1;Parent=rna-XM_006453703.1;Dbxref=InterPro:IPR006124,InterPro:IPR011258,JGIDB:Agabi_varbisH97_2_189140,GeneID:18081019,Genbank:XP_006453766.1;Name=XP_006453766.1;Note=similar to phosphoglycerate mutase;gbkey=CDS;locus_tag=AGABI2DRAFT_189140;product=hypothetical protein;protein_id=XP_006453766.1
NW_006267344.1	RefSeq	CDS	16712	16939	.	-	0	ID=cds-XP_006453767.1;Parent=rna-XM_006453704.1;Dbxref=JGIDB:Agabi_varbisH97_2_189141,GeneID:18081020,Genbank:XP_006453767.1;Name=XP_006453767.1;Note=similar to predicted protein;gbkey=CDS;locus_tag=AGABI2DRAFT_189141;product=hypothetical protein;protein_id=XP_006453767.1
NW_006267344.1	RefSeq	CDS	17976	18025	.	+	2	ID=cds-XP_006453768.1;Parent=rna-XM_006453705.1;Dbxref=JGIDB:Agabi_varbisH97_2_147179,GeneID:18079156,Genbank:XP_006453768.1;Name=XP_006453768.1;Note=similar to hypothetical protein CC1G_11406 [Coprinopsis cinerea okayama7#130];gbkey=CDS;locus_tag=AGABI2DRAFT_147179;product=hypothetical protein;protein_id=XP_006453768.1
NW_006267344.1	RefSeq	CDS	20031	20175	.	-	0	ID=cds-XP_006453769.1;Parent=rna-XM_006453706.1;Dbxref=InterPro:IPR006094,JGIDB:Agabi_varbisH97_2_181818,GeneID:18080205,Genbank:XP_006453769.1;Name=XP_006453769.1;Note=similar to predicted protein;gbkey=CDS;locus_tag=AGABI2DRAFT_181818;product=hypothetical protein;protein_id=XP_006453769.1
NW_006267344.1	RefSeq	CDS	34799	35622	.	+	2	ID=cds-XP_006453776.1;Parent=rna-XM_006453713.1;Dbxref=JGIDB:Agabi_varbisH97_2_189147,GeneID:18081023,Genbank:XP_006453776.1;Name=XP_006453776.1;gbkey=CDS;locus_tag=AGABI2DRAFT_189147;product=hypothetical protein;protein_id=XP_006453776.1
NW_006267344.1	RefSeq	CDS	40245	40511	.	+	2	ID=cds-XP_006453778.1;Parent=rna-XM_006453715.1;Dbxref=JGIDB:Agabi_varbisH97_2_113550,GeneID:18076339,Genbank:XP_006453778.1;Name=XP_006453778.1;Note=similar to hypothetical protein;gbkey=CDS;locus_tag=AGABI2DRAFT_113550;product=hypothetical protein;protein_id=XP_006453778.1
NW_006267344.1	RefSeq	CDS	46894	47138	.	-	0	ID=cds-XP_006453781.1;Parent=rna-XM_006453718.1;Dbxref=InterPro:IPR001365,JGIDB:Agabi_varbisH97_2_60662,GeneID:18085877,Genbank:XP_006453781.1;Name=XP_006453781.1;Note=similar to AMP deaminase%2C putative;gbkey=CDS;locus_tag=AGABI2DRAFT_60662;product=hypothetical protein;protein_id=XP_006453781.1
```

<details>
  <summary><code>locus_tag</code> is how NCBI identifies each gene in the genome. Match the text <code>locus_tag=</code> and all alphanumeric characters and underscores that follow.</summary>

`locus_tag=\w+`
<br><br>
Note: this would work too: `locus_tag=[\w]+`, but the `[]` isn't necessary because \w is a predefined character class.
</details>

---

<details>
  <summary><code>Parent</code> is an identifier for the an RNA sequence for this annotations. Match <code>Parent=</code> and all alphanumeric characters, underscores <code>_</code>, hyphens <code>-</code>, and periods <code>.</code>that follow</summary>

`Parent=[\w.-]+`
</details>

---

<details>
  <summary>You might have noticed that each section of the line is separated by a <code>;</code>. One common method with text structured this way is to select all text that is not that known delimiter character. The advantage of this is that you don't need to know every type of character, just the ending character. Use this method to select <code>Dbxref=</code> and all text up to the next <code>;</code></summary>

`Dbxref=[^;]+`
<br><br>
If you tried this is may seem like it work, but it actually selects everything up to the <i>last</i> `;`:
<br>
`Dbxref=.+;`
<br>
This is a common pitfall in regular expressions and in a moment we'll look at why it does this (`+` is a greedy quantifier, which is what they're called, not a value judgement).
</details>

---

### Greedy quantifiers

In the note about the final exercise, we noted that if you use `Dbxref=.+;` in the GFF example, you capture from `DBxref=` to the *last* semicolon in the line rather than the next semicolon. Regex quantifiers by default will make the longest match (called "greedy matching"), this can lead to unexpected results. In the pattern matching, when the `.+` is reached, the match extends to the end of the and then works **backwards** until a `;` is reached. This leads to the longest match being retuned.

To avoid this, you can use a "lazy quantifier" with is `+?` or `*?`. Another approach that works well is to use `[^;]+` which also finds the shortest match. Try these quantifiers and see how the matched text changes.

See the [regex wiki page](https://en.wikipedia.org/wiki/Regular_expression#Lazy_matching) for more info on this.

## Using substitution to extract information from a text string

So far in working with these test strings we have been matching portions of text. A common process for text files is to use a regular expression to leave a relevant portion of a line, and remove the surrounding text.

In the last exercise working with the GFF file we matched the value of `Dbxref=` with `Dbxref=[^;]`. How can we take the test string and remove all the surrounding text?

We'll be using the Substitution feature in regex101.com. This can be selected by choosing `Substitution` in the `FUNCTION` section on the left pane of the site.

![Choose Substitution from Function](images/Substitution.png)

TODO: Finish this example, then add fasta headers and then anchors/assertions


### Anchors/assertions `^`, `$`, `\b`

Sometimes you want to specify that a match occurs at a certain part of a line. You can use anchors or assertion characters to specify where in a line a match occurs.

The common anchors are:

```
^     Start of a line
$     End of a line
\b    Word boundary: a word character (\w) next to a word character \W
```

Test string *(copy into Regex101.com)*:
```
GAGGAAGAG
GAGGACGAG
GAGG-TGAG
GAG-AGGAG
GAG---GAG
```

<details>
  <summary>Find entire lines that contain only nucleotides, no gaps</summary>

`^[ACGT]+$`
<br>
Matches line 1 and 2.
<br><br>
The following would work too, although it might find invalid nucleotide characters in the sequence:
<br>
`^[^-]+$`
<br><br>
Note the two meanings of `^`. The first one is to anchor the beginning of the line and the second inside the brackets means not in a class.
<br><br>
The following would work too, although it would also match invalid nucleotide characters if they were in the sequence:
<br>
`^\w+$`
</details>

---

## Working with sequence headers and replacing text

A common change to make with biological data is altering the headers (sequence names) in a FASTA file. The header lines start with a `>` and are followed by lines with the sequence.

Test string *(copy into Regex101.com)*:
```
>locus-1022_NMNH-2020-06-02
GAGGAAGAGGAGGATAGAGGAGGT--GAAGAGGAGGAAGAGGTCAGGAAGAGGAGGAAGAG
>locus-301_Sp.nov.ab
GAGGAAGAGGA-GATAGAGGAGGT---AAGAGGAGGAAGAGGT-AGGAAGAGGAGGAAGAG
```

In this exercise we're going to take these headers and alter them so that the Sample ID precedes the Locus ID.

In these headers the Locus and Sample IDs are separated by an underscore `_`. The locus ID always starts with `locus-`. The Sample ID follows the `_` (but does not contain underscores or spaces)

<details>
  <summary>Part 1: Write a pattern to match the <code>></code> then everything up to and including the underscore <code>_</code></summary>

`^>.+_`
</details>

---

<details>
  <summary>Part 2: Write a pattern to match the underscore and everything beyond it</summary>

`_.+$`
</details>

---

<details>
  <summary>Part 3: Combine the patterns in Parts 1 and 2 to match the two IDs, the underscore and the starting <code>></code></summary>

`^>.+_.+$`
</details>

---

### Replacing text: capture groups `()`

To continue, we need to learn about the special tools regular expressions provide for replacing text.

We've seen how regular expressions can match a variety of text rather than the fixed text of a traditional "find." What if we need to replace the text with a portion of what was found?

Regular expressions allow us to "capture" found text and use it in the replacment. We use parentheses `()` the text we want to save and reuse in the replacement.

Let's try it with our FASTA header line pattern: `^>.+_.+$`

If we add parentheses around a portion of the pattern, say the Locus ID section: `^>(.+)_.+$` we see in regex101.com that the locus portion is in a different color and labeled "Group 1".

<details>
  <summary>Part 4: Write a pattern to match and separately capture using <code>()</code> the Locus ID and Sample ID</summary>

`^>(.+)_(.+)$`
</details>

---

#### Using the captured text `\1`, `\2` etc.

To reveal regex101.com's replacement features, choose "Substitution" under the "Function" section in the left pane.

In the replacement text box, we can enter plain text and a reference to the captured text.

The first captured text is referenced with `\1`, the second with `\2` etc. You can have up to 9 captured groups, but there are ways to have more if needed (see "Named capturing groups" in the regex101.com reference section).

<details>
  <summary>Part 5: Using the pattern from Part 4, write the replacement string to have the Sample ID precede the Locus ID with an underscore separating the two. Make sure to add the <code>></code> at the start of the header line</summary>

Search for: `^>(.+)_(.+)$`
<br><br>
Replace with: `>\2_\1`
</details>

## Extracting information from a `GFF` annotation file

A [GFF file](https://github.com/The-Sequence-Ontology/Specifications/blob/master/gff3.md) is used to annotate sequences. It is a tab-delimited text files with defined columns to describe to feature being annotated. Each row of the file is a different annotation. The last column (the 9th) is the `attributes` column, it contains additional information about the annotation that comes from the annotation pipeline that procuded the gff file.

In this portion of a [NCBI gff annotation file](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/300/575/GCF_000300575.1_Agabi_varbisH97_2/GCF_000300575.1_Agabi_varbisH97_2_genomic.gff.gz) for the [genome of the common mushroom *Agaricus bisporus*](https://www.ncbi.nlm.nih.gov/genome/?term=txid936046), listed are the a few of the 61309 CDS annotations for the genome. The `attributes` column includes the NCBI IDs of the Genes that are annotated. These IDs are given as `Name=[Gene ID];`, for example, `Name=XP_006454979.1;`

**How can we extract the IDs given after `Name=`?**

Test string *(copy into Regex101.com)*:

From [NCBI gff annotation file](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/000/300/575/GCF_000300575.1_Agabi_varbisH97_2/GCF_000300575.1_Agabi_varbisH97_2_genomic.gff.gz)

```
NW_006267344.1	RefSeq	CDS	3326	3553	.	-	0	ID=cds-XP_006453763.1;Parent=rna-XM_006453700.1;Dbxref=JGIDB:Agabi_varbisH97_2_189137,GeneID:18081018,Genbank:XP_006453763.1;Name=XP_006453763.1;gbkey=CDS;locus_tag=AGABI2DRAFT_189137;product=hypothetical protein;protein_id=XP_006453763.1
NW_006267344.1	RefSeq	CDS	8427	8432	.	-	0	ID=cds-XP_006454979.1;Parent=rna-XM_006454916.1;Dbxref=InterPro:IPR000330,InterPro:IPR001650,InterPro:IPR015194,InterPro:IPR015195,JGIDB:Agabi_varbisH97_2_175561,GeneID:18079446,Genbank:XP_006454979.1;Name=XP_006454979.1;gbkey=CDS;locus_tag=AGABI2DRAFT_175561;product=SNF2 family DNA-dependent ATPase;protein_id=XP_006454979.1
NW_006267344.1	RefSeq	CDS	9630	11399	.	+	0	ID=cds-XP_006453764.1;Parent=rna-XM_006453701.1;Dbxref=JGIDB:Agabi_varbisH97_2_113535,GeneID:18076336,Genbank:XP_006453764.1;Name=XP_006453764.1;gbkey=CDS;locus_tag=AGABI2DRAFT_113535;product=hypothetical protein;protein_id=XP_006453764.1
NW_006267344.1	RefSeq	CDS	11528	11651	.	+	0	ID=cds-XP_006453765.1;Parent=rna-XM_006453702.1;Dbxref=JGIDB:Agabi_varbisH97_2_62215,GeneID:18085933,Genbank:XP_006453765.1;Name=XP_006453765.1;Note=similar to hypothetical protein SNOG_03478;gbkey=CDS;locus_tag=AGABI2DRAFT_62215;product=hypothetical protein;protein_id=XP_006453765.1
NW_006267344.1	RefSeq	CDS	13993	14057	.	+	2	ID=cds-XP_006453766.1;Parent=rna-XM_006453703.1;Dbxref=InterPro:IPR006124,InterPro:IPR011258,JGIDB:Agabi_varbisH97_2_189140,GeneID:18081019,Genbank:XP_006453766.1;Name=XP_006453766.1;Note=similar to phosphoglycerate mutase;gbkey=CDS;locus_tag=AGABI2DRAFT_189140;product=hypothetical protein;protein_id=XP_006453766.1
NW_006267344.1	RefSeq	CDS	16712	16939	.	-	0	ID=cds-XP_006453767.1;Parent=rna-XM_006453704.1;Dbxref=JGIDB:Agabi_varbisH97_2_189141,GeneID:18081020,Genbank:XP_006453767.1;Name=XP_006453767.1;Note=similar to predicted protein;gbkey=CDS;locus_tag=AGABI2DRAFT_189141;product=hypothetical protein;protein_id=XP_006453767.1
NW_006267344.1	RefSeq	CDS	17976	18025	.	+	2	ID=cds-XP_006453768.1;Parent=rna-XM_006453705.1;Dbxref=JGIDB:Agabi_varbisH97_2_147179,GeneID:18079156,Genbank:XP_006453768.1;Name=XP_006453768.1;Note=similar to hypothetical protein CC1G_11406 [Coprinopsis cinerea okayama7#130];gbkey=CDS;locus_tag=AGABI2DRAFT_147179;product=hypothetical protein;protein_id=XP_006453768.1
NW_006267344.1	RefSeq	CDS	20031	20175	.	-	0	ID=cds-XP_006453769.1;Parent=rna-XM_006453706.1;Dbxref=InterPro:IPR006094,JGIDB:Agabi_varbisH97_2_181818,GeneID:18080205,Genbank:XP_006453769.1;Name=XP_006453769.1;Note=similar to predicted protein;gbkey=CDS;locus_tag=AGABI2DRAFT_181818;product=hypothetical protein;protein_id=XP_006453769.1
NW_006267344.1	RefSeq	CDS	34799	35622	.	+	2	ID=cds-XP_006453776.1;Parent=rna-XM_006453713.1;Dbxref=JGIDB:Agabi_varbisH97_2_189147,GeneID:18081023,Genbank:XP_006453776.1;Name=XP_006453776.1;gbkey=CDS;locus_tag=AGABI2DRAFT_189147;product=hypothetical protein;protein_id=XP_006453776.1
NW_006267344.1	RefSeq	CDS	40245	40511	.	+	2	ID=cds-XP_006453778.1;Parent=rna-XM_006453715.1;Dbxref=JGIDB:Agabi_varbisH97_2_113550,GeneID:18076339,Genbank:XP_006453778.1;Name=XP_006453778.1;Note=similar to hypothetical protein;gbkey=CDS;locus_tag=AGABI2DRAFT_113550;product=hypothetical protein;protein_id=XP_006453778.1
NW_006267344.1	RefSeq	CDS	46894	47138	.	-	0	ID=cds-XP_006453781.1;Parent=rna-XM_006453718.1;Dbxref=InterPro:IPR001365,JGIDB:Agabi_varbisH97_2_60662,GeneID:18085877,Genbank:XP_006453781.1;Name=XP_006453781.1;Note=similar to AMP deaminase%2C putative;gbkey=CDS;locus_tag=AGABI2DRAFT_60662;product=hypothetical protein;protein_id=XP_006453781.1
```

<details>
  <summary>Part 1. Write a pattern to match <code>Name=</code> and the identifier given (before the next <code>;</code>)</summary>

`Name=[^;]+`
<br><br>
or
<br><br>
`Name=[\w.]+;`
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
  <summary>Part 4. Using the pattern from Part 3, write the replacement string to replace the lines with the captured ID</summary>

Search for: `^.*Name=([^;]+).*$`
<br><br>
Replace with: `\1`
</details>

---

The ID we extracted can be incorporated into a URL that opens the NCBI gene database page for the ID. The url is formatted: `https://www.ncbi.nlm.nih.gov/gene/?term=[Gene ID]`

<details>
  <summary>Part 5. Using the pattern from Part 4, re-write the replacement string so the Gene ID is used to create a NCBI gene database URL</summary>

Search for: `^.*Name=([^;]+).*$`
<br><br>
Replace with: `https://www.ncbi.nlm.nih.gov/gene/?term=\1`
<br><br>
Try out the URLs that were created.
</details>

## Regex Resources

- A biological focused tutorial including command line tools: https://open.oregonstate.education/computationalbiology/chapter/patterns-regular-expressions/
- A recent (late 2020) Python focus regex tutorial: https://amitness.com/regex/
- Another biological focused tutorial, ths one with a Python focus: https://pythonforbiologists.com/regular-expressions
- Library Carpentires Regular Expressions lesson: https://librarycarpentry.org/lc-data-intro/
- Good place to find pre-made regular expressions for specific purposes like finding email addresses: https://www.regular-expressions.info/

Interactive games/quizes:
- https://regexone.com/
- https://regexcrossword.com/
