![](../Common_Figures/LinuxBashROOT_logos.png)


# Regular expressions

**Last update**: 20240731


### Table of Contents
1. [What is a regular expression?](#what.is.regex)
	
	* [Shell's wildcard expansion in filenames (globbing)](#globbing)
	* [Basic Regular Expression (BRE)](#bre)
	* [Extended Regular Expression (ERE)](#ere)
	* [Perl-Compatible Regular Expressions (PCRE)](#pcre)
	
2. [Metacharacters](#metacharacters)

	* [Backslash](#backslash)  ```\```	
	* [Dot](#dot)  ```.```
	* [Asterisk](#asterisk) ```*```
	* [Anchors](#anchors) ```^``` and ```$```
	* [Character classes](#character.classes) ```[ ... ]``` 
	* [Question mark](#question.mark) ```?```
	* [Plus](#plus) ```+```
	* [Repetition operator](#repetition) ```\{ ... \}``` and  ```{ ... }```
	* [Alternation operator](#alternation) ```|```
	* [Grouping operator](#grouping) ```( ... )```
	* [POSIX character classes](#POSIX.character.classes) ```[:keyword:]```
	* [Non-standard](#nonstandard) ```\<``` and ```\>``` 
	
3. [Real-life examples](#real.life.examples)

4. [Further reading](#further.reading)

   



### 1. What is a regular expression? <a name="what.is.regex"></a>
A regular expression, or _regex_ for short, is a pattern template used to filter text in order to extract specific information. Or, looking from another angle, a regular expression is a pattern that describes a set of strings. Writing a regular expression is equivalent to creating a text filter. Patterns used to define regular expression contain symbols with special, non-literal meaning. In general, such special individual symbols or specific combinations of individual symbols, are named _metacharacters_. When interpreted directly by a shell to perform filename expansion (or _globbing_), metacharacters are called _wildcards_. Some metacharacters have different meanings when used in regular expression or in filename expansion. 

Historically, the first implementation and use of regular expression can be traced back to the late 1960s and Ken Thompson's re-implementation of line-oriented editor QED for Multics (an unsuccessful operating system that predated Unix). There are four major categories of regular expressions, and in what follows next, we attempt to systematize.


#### Shell's wildcard expansion in filenames (globbing) <a name="globbing"></a>
Each shell supports the process of matching expressions containing wildcards to filenames. In fact, one of the most important features of shell is to be able to operate on multiple files simultaneously. As the basic example, in the following command input
```bash
ls *.txt
```
shell will list all files whose names end with _.txt_ in the current working directory. In the above expression, ```*``` is a _wildcard_ and ```*.txt``` is a _glob_. Basically, _glob_ is a pattern containing one or more wildcards. Its name originates from the early-days Unix command named **glob** (shortcut for 'global'), which was used by the shell to expand wildcard characters in the list of file paths, and supply back that list of files to the command. Just like any other frequently used feature, this mechanism was eventually implemented directly into the shell.

The standard and most frequent wildcards or combination of wildcards used by the shell in filename expansions are: ```?``` ```*``` ```[]``` ```[!]``` ```{}``` ```\``` 

It is important to remember that when these special shell symbols are used in regular expressions, their interpretation can be different. In addition, when specifying regex as a pattern in commands like **grep**, **find**, etc., that regex always needs to be embedded within strong quotes, schematically as ```'some-regex-with-special-symbols'```, so that these special symbols are not interpreted and expanded first by the shell as wildcards, before supplying that regex to the command.


#### Basic Regular Expressions (BRE) <a name="bre"></a>

* standardized by POSIX
* metacharacters: ```.``` ```*``` ```[]``` ```^``` ```$``` ```\``` and compound repetition operator ```\{n,m\}```  
* example programs that support BRE syntax: **ed**, **sed**, and **grep**


#### Extended Regular Expressions (ERE) <a name="ere"></a>

* standardized by POSIX
* all metacharacters supported by BRE, only with a slightly different notation for compound repetition operator ```{n,m}```
* additional metacharacters when compared to BRE: ```+``` ```?``` ```|``` ```()``` 
* first implemented by Alfred Aho in 1979 in the extended version of __grep__ called __egrep__ 
* example programs that support ERE syntax: **egrep** (or **grep -E**), **awk**, **sed -E**, operator **=~** (only in recent **Bash** versions where it can be used only within ```[[ ... ]]``` environment)

#### Perl-Compatible Regular Expressions (PCRE) <a name="pcre"></a>

* standalone library developed by Philip Hazel in 1997 and written in C ( [https://www.pcre.org/](https://www.pcre.org/) )
* the most powerful implementation of regex, aimed initially to provide all regex features available in the **perl** programming language
* use cases are rather limited in custom daily tasks, therefore not covered here in detail
* PCRE syntax for regular expressions is supported, for instance, in: **perl** (obviously), **grep -P**, etc.

In the next section, we systematically enlist all metacharacters and explain their use cases when they appear as shell wildcards in globbing, or as regex in BRE and ERE. For testing purposes, we use mostly **Bash** (globbing), **grep** (BRE) or **egrep** (ERE).






### 2. Metacharacters <a name="metacharacters"></a>

Metacharacter is a symbol, or combination of symbols, with special and non-literal meaning in regular expressions and filename expansions. Despite its peculiar name, metacharacters are present all around us. In math, we are used to using metacharacters; for instance, in the arithmetic expression ```4 * 10```, we understand that metacharacter ```*``` represents multiplication. As another example, a combination of symbols ```\n``` is a composite metacharacter and is a standard metacharacter for a new line.




#### Backslash ```\``` <a name="backslash"></a>
The backslash metacharacter ```\``` has the same meaning in globbing, BRE, and ERE. It is typically used in both directions, i.e. it turns:
1. another metacharacter into an ordinary character (this is the standard _escape mechanism_), as in:
	```bash
	$ Var=44
	$ echo $Var
	44
	$ echo \$Var
	$Var
	```
	​	 In this context, a frequent use case is ```\\```, which stands for the literal backslash character ```\```. 	

2. the ordinary characters into composite metacharacter (```\n``` is the standard composite metacharacter for a new line, ```\t``` for tab spacing, etc.):

   ```bash
   $ echo -e "a\nbb"
   a
   bb
   $ echo -e "a\tbb"
   a       bb
   ```

The frequent and distinct use case of ```\``` as a wildcard in globbing is to force literal interpretation of an empty character, instead of defaulting empty character to be input field separator. This is needed when files or directories have literal empty characters as part of their names, as this example illustrates:

```bash
$ mkdir 'Crazy name' # within strong quotes, empty character is a literal empty character 
$ ls Crazy name # here empty character is metacharacter, the default field separator
ls: cannot access 'Crazy': No such file or directory
ls: cannot access 'name': No such file or directory
$ ls Crazy\ name # OK
$ ls 'Crazy name' # OK
```

In the end, we remark that unlike backslash ```\```, slash ```/``` is not a special character, but nevertheless in some programs, like in **sed** and **awk**, it needs to be escaped due to conflict with internal syntax.



#### Dot ```.``` <a name="dot"></a>
The metacharacter dot ```.``` has a special meaning only in BRE and ERE. The reason why it doesn't have any special meaning as a wildcard in globbing originates from the fact that ```.``` as a literal character is already used to denote _hidden files_ (the files whose names begin with ```.``` , like in ".bashrc", and which are not listed by default with the __ls__ command), and to separate a filename from file extension that identifies the file format (e.g. ".txt" in "someFile.txt" to identify the plain ASCII text file). 

In regex the dot ```.``` will match any single character, except newline. The character must be present (zero occurrences do not count), and space also counts as a character. It can be thought of as a sort of "variable" in regex, in analogy with the case when a variable represents any value in an arithmetic expression. One can also say that dot ```.``` specifies a position that any character can fill. In globbing, the wildcard ```?``` has a similar meaning (see below).

Few simple examples when dot ```.``` is used as regex:

```bash
$ grep a.e <<< ace
ace
$ grep a.e <<< aze
aze
$ grep a.e <<< acce # it doesn't match, there is more than one character between "a" and "e"
$ grep a..e <<< acce
acce
$ grep a...e <<< acce # it doesn't match, there are less than three characters between "a" and "e"
```

If the literal character "." needs to be matched, like in a decimal number 2.44, then it has to be escaped:

```bash
$ cat someFile # show the content of file "someFile"
2.44
244

$ grep "\." someFile # OK, because \ is not a metacharacter within ""
2.44

$ grep '\.' someFile # OK, because \ is not a metacharacter within ''
2.44

$ grep \. someFile # WRONG!! Shell interpreted \ and what grep obtained is only metacharacter .
2.44
244

$ grep \\. someFile # OK, shell took the 2nd \ literally because it was escaped with the 1st \
2.44
```



#### Asterisk ```*``` <a name="asterisk"></a>
The metacharacter asterisk ```*``` deserves special attention because it has a different meaning when used in BRE and ERE on one side, or when used as a wildcard in globbing. Since it is used very frequently in both cases, it is very important to fully grasp the difference in its meaning, depending on the context in which it is used. Here is the summary: 

1. In BRE and ERE, asterisk ```*``` will match any number (including zero) of repetitions of the preceding character. In a more elaborate case, it will match zero or more occurrences of the preceding regular expression. By itself, the asterisk ```*``` matches nothing, it only has an effect on what appears before it. There exists a corner case when ```*``` behaves differently in BRE and ERE &mdash; as the first character of an entire BRE or after an initial `^` in BRE, asterisk ```*``` loses its special meaning, while in ERE in this case it will produce undefined results;
2. As a shell wildcard, the meaning of the asterisk ```*``` is completely different &mdash; in this context, it stands for "zero or more characters."

We first illustrate with a few examples the use of the metacharacter asterisk ```*``` in BRE and ERE.

__Example 1__: Regex ```A*E``` matches E, AE, BE, AAE, BAE, A LONG WAY HOME, etc. In each of these cases, there are "zero or more occurrences" of character A before E. 

__Example 2__: Regex ```AB*E``` matches AE, AAE, ABE, ABBE, AAE, AAEE, AABEE, etc., because in each of these cases, there are "zero or more occurrences" of character B after character A and before E. It does not match BE or BBE, because character A is missing before "zero or more occurrences" of character B. It does not match AB or ABB, because character E is missing after "zero or more occurrences" of character B.

The metacharacter asterisk ```*``` works the same way when another metacharacter is preceding it, as it is illustrated in the next examples.  

__Example 3__: Regex ```A.*E``` matches AE, ACE, AIRPLANE, A LONG WAY HOME, etc., because it matches "zero or more occurrences" of metacharacter dot ```.``` which itself stands for "any character". Therefore, compound regex ```.*``` can be interpreted as "zero or more occurrences of any characters".

__Example 4__: Regex ```".*"``` will match any string within quotes. The span matched by it is always the longest possible.

__Example 5__: Regex ```   *``` (three empty characters before ```*```) will match all lines in the text in which there are words separated by two or more empty characters, instead by default one empty character:

```bash
$ cat someFile
AAA BBB  CCC
A B C
A       BB

$ grep "   *" someFile
AAA BBB  CCC
A       BB
```

__Example 6__: In this example we demonstrate that the asterisk ```*``` by itself matches nothing:
```bash
$ grep *exam <<< exam # doesn't match, because there is no preceeding character
$ grep Y*exam <<< exam # matches, because there are zero or more occurrences of "Y" in "exam"
exam
$ grep "^*exam" <<< exam # doesn't match, this combination "^*" is the corner case, see above 
```

__Example 7__: Asterisk ```*``` an be used to elegantly diminish a difference between US and UK English in spelling:

```bash
$ cat someFile
This is color in the text.
And this in colour in the text.

$ sed -n '/colou*r/p' someFile # print lines holding both "color" and "colour"
This is color in the text.
And this in colour in the text.
````

__Example 8__: Write a regex that matches any formatting instruction in html file.

```bash
grep '<.*>' someHtml
```



Finally, we illustrate with a few separate examples the usage of metacharacter asterisk ```*``` as a wildcard in globbing, when it has a different meaning. When used as a wildcard, asterisk ```*``` stands for "zero or more occurrences of any characters". To clarify the difference, we state that wildcard ```*``` in globbing acts the same way as regex ```.*```  in BRE or ERE.

__Example 5__: TBI some text

```bash
$ touch file.pdf file_{0..3}.pdf
$ ls 
file_0.pdf  file_1.pdf  file_2.pdf  file_3.pdf  file.pdf
$ ls f*
file_0.pdf  file_1.pdf  file_2.pdf  file_3.pdf  file.pdf
$ ls file*pdf
file_0.pdf  file_1.pdf  file_2.pdf  file_3.pdf  file.pdf
$ ls *pdf
file_0.pdf  file_1.pdf  file_2.pdf  file_3.pdf  file.pdf
```



TBI 20240724 I still have to finalize the last part



#### Anchors ```^``` and ```$``` <a name="anchors"></a>

The metacharacters caret (or circumflex) ```^``` and dollar ```$``` have a special meaning only in BRE and ERE. In this context, they are the so-called _anchors_, i.e. they stand for a special position in the line or string. In particular:

1. ```^``` matches the starting position within a string or of a line;

2. ```$``` matches the ending position within a string or of a line.

For instance, regex ```^A``` will match the string ABCD, because character A is at the starting position, but it will not match BACD. Similarly, regex ```D$``` will match the string ABCD, because character D is at the ending position, but it will not match BADC:

```bash
$ grep "^A" <<< "ABCD" # OK, because A is at the starting position in string
ABCD
$ grep "^A" <<< "BACD" # doesn't match
$ grep "D$" <<< "ABCD" # OK, because D is at the ending position in string
ABCD
$ grep "D$" <<< "ABDC" # doesn't match
```

Classical example of using anchors ```^``` and ```$``` is to filter out blank lines from files. The special care has to be taken of whether blank line containts only a hidden new line metacharacter ```\n```, or in addition one or more empty spaces. 

__Example 1__: Filter out all blank lines from file, assuming there are no one or more empty spaces on those lines. 
```bash
awk '!/^$/' file
sed '/^$/d' file
grep -v '^$' file
```

__Example 2__: Filter out all blank lines from file (i.e. lines which contain only a hidden new line metacharacter ```\n```), where each line can in addition contain also some empty spaces. 
```bash
awk '!/^ *$/' file
sed '/^ *$/d' file
grep -v '^ *$' file
```

Summary of further standard use cases of anchors:

* ```    *$``` &mdash; matches lines with one or more empty characters at the end (there have to be two or more spaces before ```*``` )
* ```^  *``` &mdash; matches a line with one or more leading spaces (there have to be two or more spaces before ```*``` )
* ```^.*$``` &mdash; matches the entire line



TBI 20240725 Write a bridge at the end



#### Character classes ```[ ... ]``` <a name="character.classes"></a>

Character classes ```[ ... ]``` (or sometimes being referred to as _bracket expression_) work the same way in globbing, BRE and ERE, and they provide a more specific variant of ```.``` metacharacter. Basically, ```.``` metacharacter would match any single character, while ```[ ... ]``` matches any single character enlisted between ```[``` and ``` ]```.

```bash
$ echo abc | grep "a[bx]c"
abc
$ echo axc | grep "a[bx]c"
axc
$ echo ayc | grep "a[bx]c" # doesn't match, "y" is not enlisted between "[" and "]"
```

A very handy use case is to tolerate common misspellings when looking for a particular pattern:

```bash
$ cat someFile
maintanance
maintenance
maintanence
maintenence

$ grep "maint[ea]n[ea]nce" someFile
maintanance
maintenance
maintanence
maintenence
```

Another common use case is to ignore in the search if the word is at the begininng of the sentence, and therefore capitalized, or not.
```bash
$ echo someWord | grep "[sS]omeWord"
someWord
$ echo SomeWord | grep "[sS]omeWord"
SomeWord
```

Usage of character classes ```[ ... ]``` in globbing is the same:
```bash
$ ls
someFile_1.txt	someFile_2.txt	someFile_3.txt	someFile_4.txt
$ ls someFile_[23].txt
someFile_2.txt	someFile_3.txt 
```

Inside ```[ ... ]``` the standard metacharacters loose their special meaning:
```bash
$ echo '$Var' | grep '[$?]ar'
$Var
$ echo '.file' | grep '[.]file'
.file
```

However, the notation ```[ ... ]``` supports two of its own metacharacters: ```-``` and ```^```.  The symbol ```-``` is a metacharacter within ```[ ... ]``` only if it does not appear at the first or at the last position &mdash; at any other position it has a special meaning and it indicates the _range_ between two surrounding symbols.

```bash
$ touch file_A.txt file_B.txt file_C.txt file_D.txt	file_E.txt 
$ ls file_[B-D]
file_B.txt	file_C.txt 	file_D.txt 
```

__Example__: Write the regex which matches any of the 4 arithmetic operators: "+", "-", "*", and "\\". 

- ```[-+*\]``` and ```[+*\-]``` are both correct. Within ```[ ... ]```, the symbol ```-``` at the very beginning or at the very end is not a metacharacter;
- ```[+-*\]``` is wrong, because here the symbol ```-``` is in the middle, and is interpreted as a metacharacter which indicates range between symbols "+" and "*", which is ill-defined

```bash
TBI 20240726 Related to this example In awk, this is fine:  [+\-*\] ⇒ but who can read this now? TBI test this
```

Multiple ranges are also fine, e.g.

```bash
$ touch file_A.txt file_B.txt file_C.txt file_D.txt file_1.txt file_2.txt file_3.txt file_4.txt
$ ls file_[A-C2-4].txt
file_2.txt  file_3.txt  file_4.txt  file_A.txt	file_B.txt	file_C.txt
```

On the other hand, the symbol ```^``` (_circumflex_ or _caret_) is a metacharacter within ```[ ... ]``` only if it appears at the very first position &mdash; at any other position, including the last position, it doesn't have any special meaning. Therefore, the regex ```[^...]``` has the following special meaning: ```^``` in the first place excludes all following characters within ```[ ... ]``` from being matched. For instance:

```bash
$ touch file_A.txt file_B.txt file_C.txt file_D.txt file_E.txt file_F.txt file_G.txt file_H.txt
$ ls file_[^ACF].txt
file_B.txt	file_D.txt 	file_E.txt 	file_G.txt 	file_H.txt 
```

Only in globbing, the synonym for regex ```[^...]``` is ```[!...]```. But because the notation ```[!...]``` does not have any special meaning in BRE or in ERE, and to avoid confusion, we recommend the usage only of regex ```[^...]``` in any context.

Finally, we need to clarify how the characters ```[``` and ```]``` themselves are treated within character classes ```[ ... ]``` . The character ```]``` as the first character within is just a character (i.e. not a metacharacter, like when it’s on any other place). The same applies to character ```[``` , as the following example illustrates:

```bash 
$ grep "[]]" <<< "]"
]
$ grep "[[]" <<< "["
[
```

Character classes ```[ ... ]``` can be naturally combined with other metacharacters, for instance:

- `[ab]c*` — matches “ab”, “abc”, “abcc”, but also "a", "b", "ac", "acc", "bc", "bcc", etc.
- `[ab]cc*` — matches "ac", "bc", “abc”, “abcc”, “abccc”, but not  "a", "b", “ab”, etc.

In the above example, asterisk ```*``` had an effect only on a single preceding character "c". But we can make asterisk ```*``` acting directly on character classes ```[ ... ]``` , when the final result is different. In combination with character classes, ```*``` matches any number of characters in that class, but also in any order:

- `[no]*` — matches "n", "nn", "nnn", "o", "oo", "ooo", "no", "nno", "noo", "on", "oon", "onn", etc. It will match also "abc" because that would correspond to "zero occurences either of "n" or "o". TBI 20240728 this is not really a usefuly regex . re-think if I nedd this example at all





#### Question mark ```?``` <a name="question.mark"></a>

The metacharacter question mark ```?``` is not supported in BRE. When used in ERE or when used as a wildcard in globbing it has a different meaning: 

1. In ERE, the question mark ```?``` matches zero or one occurrences of the preceeding character or of the preceeding regex. Therefore, ```?``` makes the preceeding character or regex optional. By itself, the question mark ```?``` matches nothing, it only has an effect on what appears before it. There exists a corner case when ```?``` behaves differently &mdash; as the first character of an entire ERE or after an initial `^` in ERE, the question mark ```?``` produces undefined results;
2. As a shell wildcard, the question mark ```?``` stands for "any single character". Therefore, metacharacter ```?``` in globbing acts the same way as metacharacter ```.``` in BRE and ERE.

We first illustrate the usage of question mark ```?``` in ERE:

```bash
$ egrep "ab?c" <<< "ac" # matches, because "ac" (zero occurences of "b") matches "ac"
ac
$ egrep "ab?c" <<< "abc" # matches, because "abc" (one occurence of "b") matches "abc"
abc
$ egrep "ab?c" <<< "abbc" # doesn't match, because neither "ac" nor "abc" match "abbc"
$ egrep "b?c" <<< "bbc" # matches, because both "c" and "bc" match "bbc"
bbc
$ egrep "ab?c" <<< "abcc" # matches, because "abc" (one occurence of "b") matches "abcc"
abcc
$ egrep "ab?c" <<< "acc" # matches, because "ac" (zero occurences of "b") matches "acc"
acc
```

From the above example, one can easily deduce the simple technique how to interpret this metacharacter in ERE in practice: since ```?``` matches zero or one occurrences, it is feasible to expand regex containing ```?``` into one or two literal strings it has to match. For instance, regex ```ab?c``` expands into two literal strings, namely "ac" or "abc", that this regex has to match.

In BRE, the question mark ```?``` is not a metacharacter:

```bash
$ grep "ab?c" <<< "ac" # doesn't match, in BRE "?" is a literal character
$ grep "ab?c" <<< "ab?c" # matches, in BRE "?" is a literal character
ab?c
```

In globbing, the question mark ```?``` stands for "any single character":

```bash
$ touch file_0.log file_A.log file_10.log file_AB.log
$ ls file_?.log
file_0.log  file_A.log
$ ls file_??.log
file_10.log  file_AB.log
$ ls file_???.log # doesn't match any file, globbing failed
ls: cannot access 'file_???.log': No such file or directory
```

The metacharacter ```?``` can be readily combined with other metacharacters. For instance, regex ```[xy]?``` will match the case when "x" or "y" are optional, i.e. they must appear zero times or only once either of them:

```bash
$ egrep "a[xy]?b" <<< "abc" # matches, because "ab" (neither "x" or "y" appear) matches "abc" 
abc
$ egrep "a[xy]?b" <<< "axb" # matches, because "axb" (one occurence of "x") matches "axb"
axb
$ egrep "a[xy]?b" <<< "ayb" # matches, because "ayb" (one occurence of "y") matches "ayb"
ayb
$ egrep "a[xy]?b" <<< "axxb" # doesn't match, because none of "ab", "axb", "ayb" match "axxb"
$ egrep "a[xy]?b" <<< "axyb" # doesn't match, because none of "ab", "axb", "ayb" match "axyb"
$ egrep "a[xy]?" <<< "axxb" # matches, because both "a" and "ax" match "axxb"
axxb
$ egrep "a[xy]?" <<< "ax" # matches, because both "a" and "ax" match "ax"
ax
$ egrep "a[xy]?" <<< "a" # matches, because "a" matches "a"
a
$ egrep "a[xy]?" <<< "x" # doesn't match, because none of "a", "ax", "ay" match "x"
```

In the same spirit, regex `80[234]?86` would match 80286, 80386, 80486, but also 8086. 

Finally, there is a corner case, i.e. when ```?``` appears as the first character of an entire ERE or after an initial `^` in ERE, which leads, according to the POSIX standard for ERE, to undefined results:

```bash
$ egrep "?exam" <<< exam # undefined behaviour, here it matches, but this is not guaranteed to work
exam
$ egrep "^?exam" <<< exam # undefined behaviour, here it matches, but this is not guaranteed to work 
exam
```





#### Plus ```+``` <a name="plus"></a>

The metacharacter ```+``` has a special meaning only in ERE, while in BRE and globbing it is only a literal character. In ERE, its meaning can be summarized as follows: the preceding character or regex can appear one or more times but must be present at least once. Therefore, ```+``` makes the occurrence of preceeding character or regex mandatory. By itself, the plus ```+``` matches nothing, it only has an effect on what appears before it. 

Similarly and for the ```?``` metacharacter described above, one can easily deduce the simple technique how to interpret this metacharacter in ERE in practice: since ```+``` matches at least one occurrence, one expands regex containing ```+``` into one or more occurrences of preceeding character or regex it has to match. For instance, regex ```ab+c``` expands into strings "abc", "abbc", "abbbc", ...,  that this regex has to match. This is illustrated with a few examples:

```bash
$ egrep "ab+c" <<< "ac" # doesn't match, because "abc", "abbc", ..., doesn't match "ac"
$ egrep "ab+c" <<< "abc" # matches, because "abc" matches "abc"
abc
$ egrep "ab+c" <<< "abbc" # matches, because "abbc" matches "abbc"
abbc
$ egrep "ab+c" <<< "abb" # doesn't match, because "abc", "abbc", ..., doesn't match "abb"
```
This metachatacter is frequently used to search for extra spacing in the text between the words, and we illustrate the comparison with asterisk ```*``` used in ERE in the same context:

- ```   +``` (exactly two spaces followed by "+") &mdash; matches all cases when between two words there are two or more space

- ```   *``` (exactly two spaces followed by "*") &mdash; matches all cases when between two words there is one or more spaces

The metacharacter ```+``` can be combined with other metacharacters, to make a more sophisticated regex. For instance, regex ```[xy]+``` will match the case when "x" and/or "y" appear at least once, in any order: TBI 20240731 check the last statement

```bash
$ egrep "a[xy]+b" <<< "abc" # doesn't match, because none of "axb", "ayb", "axxb", "ayyb", ..., matches "abc" 
$ egrep "a[xy]+b" <<< "axb" # matches, because "axb" (one occurence of "x") matches "axb"
axb
$ egrep "a[xy]+b" <<< "ayb" # matches, because "ayb" (one occurence of "y") matches "ayb"
ayb
$ egrep "a[xy]+b" <<< "axxb" # matches, because "axxb" (more than one occurence of "x") matches "axxb"
axxb
$ egrep "a[xy]+b" <<< "axyb" # matches, because "axyb" matches "axyb"
axyb
$ egrep "a[xy]+b" <<< "ayxb" # matches, because "ayxb" matches "ayxb"
aybb
$ egrep "a[xy]+b" <<< "axxyb" # matches, TBI finalize explanation
axxyb
$ egrep "a[xy]+b" <<< "axyxyb" # matches, TBI finalize explanation
axyxyb
$ egrep "a[xy]+" <<< "axxb" # matches, because both "ax" and "axx" matches "axxb"
axxb
$ egrep "a[xy]+" <<< "ax" # matches, because "ax" matches "ax"
ax
$ egrep "a[xy]+" <<< "a" # doesn't match, because none of "ax", "ay", "axx", "ayy", ..., matches "a"
a
$ egrep "a[xy]+" <<< "x" # doesn't match, because none of "ax", "ay", "axx", "ayy", ..., matches "x"
```





TBC 20240831





#### Repetition operator ```\{ ... \}``` and  ```{ ... }``` <a name="repetition"></a>

The notation supported in ERE is ```{ ... }```, while notation ```\{ ... \}```  is used in BRE. For simplicity of notation, here only examples using ERE will be demonstrated, but they remain valid also in BRE, just each curly brace has to be escaped with backslah ```\```. 

Regex ```{n,m}``` matches a range of occurrences of single character that immediately preceeds it (even if that character is regex itself), from ```n``` times to ```m``` times (lower and upper boundaries included).

o specify a limit on repeatable regex - interval

o two formats for the interval (```n``` and ```m``` are integers between 0 and 255 TBI check these boundaries):

1. ```{m}``` - regex appears exactly m times
2. ```{m,}``` - regex appears at least m times
3. ```{,n}``` - regex appears at maximum n times
4. ```{m,n}``` - regex appears at least m times, at maximum n times



Examples (using ERE, therefore ```egrep``` below instead of ```grep```):

```bash
$ echo "abc" | egrep "ab{2}c" # doesnt' match, only one occurence of preceeding character "b"
$ echo "abbc" | egrep "ab{2}c" # matches, two occurences of preceeding character "b"
abbc
$ echo "abbbc" | egrep "ab{2}c" # doesn't match, more than two occurence of preceeding character "b"
$ echo "abbbc" | egrep "ab{2,}c" # matches, at least two occurences
abbbc
$ echo "abbbbc" | egrep "ab{2,3}c" # doesn't match
```

Example: `10{2,4}1` matches 1001, 10001, 100001, but not 101 and 1000001   TBI check and re-format

- Example: `[0-9]{3}` is the same as `[0-9][0-9][0-9]`    TBI check and re-format



o can be used also in combination with character classes [...]

o gawk supports this regex only if flag --re-interval is used



Shell wildcard -- TBI see https://tldp.org/LDP/GNU-Linux-Tools-Summary/html/x11655.htm:

{ } (curly brackets)

terms are separated by commas and each term must be the name of something or a wildcard. This wildcard will copy anything that matches either wildcard(s), or exact name(s) (an “or” relationship, one or the other).

For example, this would be valid:

```
cp {*.doc,*.pdf} ~
```



This will copy anything ending with .doc or .pdf to the users home directory. Note that spaces are not allowed after the commas (or anywhere else).

The repetion operator in regex, is not to be confused with brace expansio, TBI finalize and add few examples



o has no meaning by itself

o example:

^G[o]{2,}gle

" matches Google, Gooogle, etc.

? => find 0 or 1 <=> so ? is a shortcut for {0,1}  **BEAUTIFUL**

- => find 1 or more <=> so + is a shortcut for {1,}  **BEAUTIFUL  AB**

- => find 0 or more <=> so * is a shortcut for {0,}    **BEAUTIFUL AB**

o examples:

grep -E '^G[o]?gle' <<< "Ggle"

Ggle

grep -E '^G[o]?gle' <<< "Gogle"

Gogle

grep -E '^G[o]?gle' <<< "Google"

nothing

grep -E '^G[o]*gle' <<< "Ggle"

Ggle

grep -E '^G[o]*gle' <<< "Gogle"

Gogle

grep -E '^G[o]*gle' <<< "Google"

Google

grep -E '^G[o]*gle' <<< "Gooogle"

Gooogle

grep -E '^G[o]+gle' <<< "Ggle"

nothing

grep -E '^G[o]+gle' <<< "Gogle"

Gogle

grep -E '^G[o]+gle' <<< "Google"

Google

grep -E '^G[o]+gle' <<< "Gooogle"

Gooogle

o for a single character like above, [o] can be replaced with o **TBI AB check further**

o for a compound expressions, use ( ) (can be nested, like braces in math!), and then { }

$ grep -E '(\b(an|the)\ ){2,}' <<< "an an the the"

an an the the





#### Alternation operator ```|``` <a name="alternation"></a>

Alternation operator ```|``` is supported in ERE and it stands for logical OR in regex. Schematically, it is used as follows

```bash
regex-1|regex-2|...
```

For instance:

```bash
$ echo "cat sleeps" | egrep "cat|dog"
cat sleeps
$ echo "dog sleeps" | egrep "cat|dog"
dog sleeps

# Note the difference with respect to default grep: | vs. \|
$ echo "cat sleeps" | grep "cat|dog"
$ echo "cat sleeps" | grep "cat\|dog"
cat sleeps
```


o not to be confused with pipe symbol TBI finalize + see if I can come with the better example, as above I do use | both for pipe and OR



`|` — alternation. Either a preceeding or following regex can be matched

- specifies union of regular expressions

- Example: 

  ```
  UNIX|LINUX|BSD
  ```

   — matches lines which contain either UNIX or LINUX or BSD

  - Remember, this is not supported by sed



yields a match if any of the patterns match

grep -E '^(first|second|third)' someFile

o this is the same thing:

grep -E '^(bat|Cat)' someFile

grep -E '^[bC]at' someFile



#### Grouping operator ```( ... )``` <a name="grouping"></a>

We have already seen that a value can be stored in a variable by explicit assignment (using the operator 



`()` — groups regular expressions

- Example: `Big( Computer)?` — matches both “Big” and “Big Computer” **TBI test**
- Example: `compan(y|ies)` — matches both “company” and “companies”
- Example: `(^| )` — matches beginning of the line, or space



(abc|def)+ => match a string that contains one or more occurrences of substrings 'abc' and 'def'



o the group is treated like a standard character

```bash
$ echo "Sat" | egrep "Sat(urday)?"
Sat
$ echo "Saturday" | egrep "Sat(urday)?"
Saturday
```





#### POSIX character classes ```[:keyword:]``` <a name="#POSIX.character.classes"></a>

POSIX **character classes** are keywords bracketed by ```[:``` and ```:]``` , which  must appear inside the square brackets, ```[``` and ```]```,  of a bracket expression. TBI this doesn't read well at all

o BRE supports the following (TBI check still for globbing and ERE):

o The POSIX standard defined regular expression characters and operators, both for BRE and ERE



[[:alpha:]] -- alphabetic characters a, b, ..., z (both lowercase and uppercase)

[[:lower:]] -- lowercase alphabetic characters a, b, ..., z

[[:upper:]] -- uppercase alphabetic characters A, B, ..., Z

[[:alnum:]] -- alphanumeric characters a, b, ..., z (both lower- and upper-cases), 0, 1, ...,  9 TBI check this one further, because in Table 3-3 the claim "printable characters", but I do not see that working

[[:blank:]] -- space or tab

[[:space:]] -- Whitespace characters tab, \n, formfeed, vertical tab, carrige return (TBI check this one in practice)

[[:digit:]] -- numeric characters 0, 1, ..., 9. Same as ```[0-9]``` TBI check if it's really the same, are there any corner cases? 

[[:punct:]] -- punctuation characters ; , : . ! ? etc. TBI check this one and provide the full list

[[:print:]] -- all printable characters

[[:graph:]] -- printable and visible (non-space) characters (TBI check this one)

[[:cntrl:]] -- control characters (TBI check this one, drop perhaps?)

[[:xdigit:]] -- hexadecimal digits (TBI check this one, drop perhaps?)



#### Non-standard ```\<``` and ```\>``` <a name="#nonstandard"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 

GNU versions of sed, awk and grep support also \```\\<``` and ``` \\>``` for matching the string and the beginning and end of the word. Use with care, limited portability (AB)






### 3. Real-life examples <a name="real.life.examples"></a>
Frequent point of failure when writing a shell script is to forget that **Bash** will always by default attempt to perform wildcard expansion, i.e. it will attempt to match any glob against files in the current directory, and replace it by a list of filenames. For instance, consider the line:

```bash
echo Is this my file? # WRONG!!
```

The last argument is a glob, and **Bash** will match it against all files in the current directory which starts with "file" and have exactly one more character. If it happens that in the current directory there are files with names _file0_ and _file1_, what echo receives eventully as arguments is:

```bash
echo Is this my file0 file1
```

To prevent such unwanted surprises, simply use quote:

```bash
echo "Is this my file?"
```

Within quotes, ```?``` is not a metacharacter, i.e. it's literally a good old question mark. While this example was not dangerous, this one can lead to disaster:

```bash
unset myArray[1] # WRONG!!
```

Here, argument is treated as a glob, because it has metacharacters ```[]``` , and it will be matched against a file named _myArray1_ in the current working directory, if it exists. Therefore, the correct version here is also 

```bash
unset "myArray[1]"
```

Within quotes, ```[]``` is not treated as a glob.

**Common idioms:**

* ```(.*)``` &mdash; match any sequence of characters enclosed in parentheses
* ``` [:alnum:]``` or ```[A-z0-9]``` &mdash; match any alpha-numeric character  =>  TBI 20240721 test
* ``` [^[:alnum:]]``` or ```[^A-z0-9]``` &mdash; match anything, except alpha-numeric character  =>  TBI 20240721 test
* ```X?``` &mdash; match no or one capital letter X
* ```X*``` &mdash; match zero or more capital letters X
* ```X+``` &mdash; match one or more capital letter X
* ```X{n}``` &mdash; match exactly n constitutive capital X's (use with grep -E)
* ```X{n,}``` &mdash; match at least n constitutive capital X's
* ```X{n,m}``` &mdash; match at least n and not more than m constitutive capital X's



Example:  [[:alpha:]!] — matches alphabetic characters and !



grep -E -v '^$' someFile # filter out empty lines



__Example__: Write a code snippet which matches strings against globs.

```bash 
case $file in
    *.txt) process-as-text "$file ;;
    *.png) ... ;;
esac

or

if [[ $file = *.txt ]]; then
    process-as-text "$file"
elif [[ $file = *.png ]]; then
    ...
fi
```





**Use here as examples, or set as possible homeworks**

1. Write a regex which will match timestamps written in one of the following 3 formats:

   ```bash
   DD-MM-YYYY
   DD/MM/YYYY
   DD.MM.YYYY
   ```

2. Write a regex that matches ORCID format — see Wikipedia entry for definition: https://en.wikipedia.org/wiki/ORCID





### 4. Further reading <a name="further.reading"></a>

* _"Linux Command Line and Shell Scripting Bible"_, Richard Blum, Christine Bresnahan 
   * Chapter 20: Regular Expressions
* _"sed & awk"_, Dale Dougherty, Arnold Robbins 
  * Chapter 3: Understanding Regular Expression Syntax
* _"UNIX A History and a Memoir"_, Brian Kernighan
  * Section 4.6: Regular expressions 
* Linux manual page:
   * [glob][https://man7.org/linux/man-pages/man7/glob.7.html] ( or execute locally: ```$ man 7 glob``` )
   * [regex][https://man7.org/linux/man-pages/man7/regex.7.html] ( or execute locally: ```$ man 7 regex``` )
* POSIX standard
   * [Chapter 9: "Regular Expressions"][https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap09.html]
* Online regex checker: https://regex101.com/