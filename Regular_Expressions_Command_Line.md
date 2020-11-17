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
