![](../Common_Figures/LinuxBashROOT_logos.png)


# Regular expressions

**Last update**: 20240323


### Table of Contents
1. [What is a regular expression?](#what.is.regex)
	* [Shell's wildcard expansion in filenames (globbing)](#globbing)
	* [Basic Regular Expression (BRE)](#bre)
	* [Extended Regular Expression (ERE)](#ere)
	* [Perl-Compatible Regular Expressions (PCRE)](#pcre)
2. [Metacharacters](#metacharacters)
	
	* [Asterisk](#asterisk) ```*```
	* [Backslash](#backslash)  ```\```
	* [Dot](#dot)  ```.```
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
5. 

	





### 1. What is a regular expression? <a name="what.is.regex"></a>
A regular expression, or _regex_ for short, is a pattern template used to filter text in order to extract the specific information. Or looking from another angle, a regular expression is a pattern that describes a set of strings. Writing a regular expression is equivalent to creating a text filter. Patterns used to define regular expression contain symbols with special, non-literal meaning. In general, such special individual symbols or specific combinations of individual symbols are named _metacharacters_. When interpreted directly by shell to perform filename expansion (or _globbing_), metacharacters are called _wildcards_. To add to the confusion, some metacharacters have different meaning when used in regular expression or in filename expansion. Historically, the first implementation and use of regular expression can be traced back to the late 1960s and Ken Thompson's re-implementation of line-oriented editor QED for Multics (unsuccessful operating system which predated Unix). There are four major categories of regular expressions, and in what follows next, we attempt to systematize.


#### Shell's wildcard expansion in filenames (globbing) <a name="globbing"></a>
Each shell supports the process of matching expressions containing wildcards to filenames. As the basic example, in the following command input
```bash
ls -al *.txt
```
shell will list all files whose names end with  _.txt_ in the current directory. In the above expression, ```*``` is a _wildcard_ and ```*.txt``` is a _glob_. Basically, glob is a pattern containing one or more wildcards. Its name originates from the early-days Unix command named **glob** (shortcut for 'global'), which was used by shell to expand wildcard characters in the list of file paths, and supply back that list of files to the command. Just like any other frequently used feature, the glob mechanism was eventually implemented directly into the shell.

The most frequent wildcards are: ```?``` ```*``` ```[]``` 

It is important to remember that when these special symbols are used in regular expressions, their interpretation can be different. Therefore, in the next section we enlist systematically all special characters, and explain their use cases separately in regular expressions and in filename expansion, whenever the use case differs.






  * works with filenames, but perhaps also in some other context
    * ? => any single character
      * \*  => any string of characters
        * [set] (if you want - to be part of set, put it first or the last, otherwise it indicates range)
          * [!set] (! only at the very first place has a special meaning, you can either escape it, or place it at some other place)
      * ranges are not cross-platform independent
    * ~ fits also here
    * very handy with examples with pathname expansions
    * expand only to the files or directories which exists => more general is brace expansion (see below)
    * cannot be nested








#### Basic Regular Expression (BRE) <a name="bre"></a>

* standardized by POSIX
* metacharacters: ```.``` ```*``` ```[]``` ```^``` ```$``` ```\``` and compound repetition operator ```\{n,m\}```  
* programs which support it: **ed**, **sed**, and **grep**


#### Extended Regular Expression (ERE) <a name="ere"></a>

* standardized by POSIX
* all metacharacters supported by BRE, only with a slightly different notation for compound repetition operator ```{n,m}```
* additional metacharacters when compared to BRE: ```+``` ```?``` ```|``` ```()``` 
* programs which support it: **egrep** (or **grep -E**), **awk**, **sed -E**, operator **=~** in recent versions of **Bash**

#### Perl-Compatible Regular Expressions (PCRE) <a name="pcre"></a>

* standalone library written in C (  [https://www.pcre.org/](https://www.pcre.org/) ), the most powerful implementation of regex, aimed initially to provide all regex features available in **perl** programming language 
* use cases are rather limited in custom daily tasks, therefore not covered here in detail
* PCRE syntax for regular expressions are supported in: **perl** (obviously), **grep -P**

In what follows next, we enlist and briefly discuss all metacharacters which appear in globbing, BRE and ERE.






### 2. Metacharacters <a name="metacharacters"></a>

Metacharacter is a symbol, or combination of symbols, with special and non-literal meaning in regular expressions and filename expansions. Despite its peculiar name, metacharacters are present all around us. In math we are used to using metacharacters, for instance in the arithmetic expression ```4 * 10``` we understand that metacharacter ```*``` represents multiplication. As another example, combination of symbols ```\n``` is a composite metacharacter, and is common metacharacter for new line.



#### Asterisk ```*``` <a name="asterisk"></a>
1. In BRE it will match any number (including zero) of repetitions of the preceeding character or, in a more elaborate case, it will match zero or more occurrences of the preceding regular expression. By itself, asterisk "*" matches nothing, it modifies what goes before it 

2. As a shell wildcard, meaning of asterisk "*" is completely different -- in this context it stands for "zero or more characters"

Example: Regular expression for "any number of characters (including 0 characters!)" is .*

Example: Regex A.*E matches ACE, AIRPLANE, A LONG WAY HOME, etc., but also it matches AE


- When it’s applied on a single character, that character may be there or not, and if it is, there may be more than one of them
	
    - `[ab]c*` — matches “ab”, “abc”, “abcc” **(TBI this is my example, test it)**
    - `[ab]cc*` — matches “abc”, “abcc”, “abccc”, but not “ab” **(TBI this is my example, test it)**
- Classical examples:
    - `*` — matches one or more spaces
    - `.*` — matches any number of any characters in regex (in filename expansion, the same effect is achieved just with a bare asterisk*). For instance, in regex "A.*E" will match "AE" "ABE", "ABBE", "A E", "AB BE BE", etc.   
    - `".*"` — matches any string within quotes. The span matched by it is always longest possible
    - "^  *.*" -- match a line with one or more leading spaces TBI do i move this one AFTER I introduce "^"? TBI Empty characters are not shown on the RHS... 
    - `grep '<.*>' someHtml` — matches any formatting instruction in html
    - `[no]*` — in combination with character classes, it matches any number of characters in that class, but also in any ordeer. So this would match no, nno, noo, , on, oon, onn, etc.  **(TBI test it)**
- Closure ⇒ the ability to match “zero or more” of something

Note the difference here:
```bash
$ grep *exam <<< exam # doesn't match
$ grep .*exam <<< exam # matches
exam
```

Finally, it can be used to elegantly diminish a difference between US and UK English in spelling:

```bash
sed -n '/colou*r/p' # TBI prints lines holding both "color" and "colour"
````



#### Backslash ```\``` <a name="backslash"></a>
The backslash metacharacter ```\``` is typically used both directions:
1. It turns another metacharacters into ordinary character, as in:
```bash
$ Var=44
$ echo $Var
44
$ echo \$Var
$Var
```

2. It turns ordinary character into compund metacharacter (```\n``` is the standard compound metacharacter for new line)

TBI do I comment on its meaning in globbing?




#### Dot ```.``` <a name="dot"></a>
In BRE the dot ```.``` will match any single character, except newline. The character must be there (zero occurrences do not count), and space also counts as a character! It can be thought of as a sort of "variable" in regex, in analogy with the case where variable represents any value in arithmetic expression. It specifies a position that any character can fill. (TBI it seems that in awk dot matches also embedded new line => check)

```bash
$ grep a.e <<< ace
ace
$ grep a.e <<< aze
aze
$ grep a.e <<< acce # it doesn't match, more than one character
$ grep a..e <<< acce
acce
$ grep a...e <<< acce # it doesn't match, less that three characters
```

If we need to match the literal character ".", like in a decimal number 2.44, then it has to be escaped:
```bash
$ cat someFile
2.44
244

$ grep "\." someFile # OK
2.44

$ grep '\.' someFile # OK
2.44

$ grep \. someFile # WRONG!! Shell already interpreted \ TBI check here the explanation
2.44
244
```


Example #1: Demonstrate that regex "Chapter." matches Chapter anywhere, except at the end of the line, because `.` doesn’t match end of line (TBI finalize this example)

TBI do I comment on its meaning in globbing? As far as I know, it doesn't have any special meaning, but check further.



#### Anchors ```^``` and ```$``` <a name="anchors"></a>

```^``` match the empty string that occurs at the beginning of a line or string

```$``` match the empty string that occurs at the end of a line  (TBI 20240324 in awk, it matches the end of the string => test this)

Classic example: Filter out empty lines

```bash
awk '!/^$/' file
sed '/^$/d' file
grep -v '^$' file
grep -v ^$ file
```

Further examples:

```   *$```  -- matches lines with one or more empty characters at the end 

``` ^$``` -- matches blank lines

```^ *$``` -- matches blank line in a file even if it contains some spaces

```^.*$``` -- matches the entire line

Important: in sed and grep, ```^``` and ```$``` are metacharacters only when at first and last place, respectively, in regex. In awk, they are always metacharacters in regex, irrespectively of their position- TBI 20240323 test this



#### Character classes ```[ ... ]``` <a name="character.classes"></a>

Character classes ```[ ... ]``` work the same way in globbing, BRE and ERE, and they provide a more specific variant of ```.``` metacharacter. Basically, ```.``` metacharacter would match any single character, while ```[ ... ]``` matches any single character enlisted between ```[``` and ``` ]```.

```bash
$ echo abc | grep "a[bx]c"
abc
$ echo axc | grep "a[bx]c"
axc
$ echo ayc | grep "a[bx]c" # doesn't match, "y" is not enlisted between [ and ]
```


A very handy use case is to tolerate common mispellings, when looking for a particular pattern:

maint[ea]n[ea]nce
l[au]nch

TBI build a concrete example for 2 above

Another common use case is to ignore in the search if the word was at the begininng of the sentence, and therefore capitalized, or not.
```bash
$ echo someWord | grep "[sS]omeWord" # matches
someWord
$ echo SomeWord | grep "[sS]omeWord" # matches
SomeWord
```

Example for globbing:
```bash
$ ls
someFile_1.txt	someFile_2.txt	someFile_3.txt	someFile_4.txt
$ ls someFile_[23].txt
someFile_2.txt	someFile_3.txt 
```

Ranges are also supported with "-", for instance:
```bash
$ ls
someFile_1.txt	someFile_2.txt	someFile_3.txt	someFile_4.txt
$ ls someFile_[1-3].txt
someFile_1.txt	someFile_2.txt	someFile_3.txt 
```

Multiple ranges are also fine, e.g.

```bash
$ ls
file_A.txt	file_B.txt	file_C.txt	file_D.txt	file_E.txt	file_F.txt	file_G.txt	file_H.txt
$ ls file_[A-CF-H].txt
file_A.txt	file_B.txt	file_C.txt 	file_F.txt 	file_G.txt 	file_H.txt 
```

Inside ```[ ... ]``` the standard metacharacters loose their special meaning
```bash
$ echo '$Var' | grep '[$?]ar'
$Var
$ echo '.file' | grep '[.]file'
file
```

However, the notation ```[ ... ]``` supports two of its own metacharacters: ```-``` and ```^```. 
The symbol ```-``` is a metacharacter within ```[ ... ]``` only if it does not appear at the first or at the last position -- at any other position it has a special meaning and it indicates the range between two surrounding symbols.

```bash
$ touch file_A.txt	file_B.txt	file_C.txt	file_D.txt	file_E.txt	file_F.txt	file_G.txt	file_H.txt # or use in Bash brace expansion: touch file_{A..H}.txt
$ ls file_[B-D]
file_B.txt	file_C.txt 	file_D.txt 
```

Example: Write the regex which matches any of the 4 arithmetic operators + - * \ 

```bash
[-+*\] # Correct. Within [ ... ], - at the beginning is not a metacharacter 
[+*\-] # Correct. Within [ ... ], - at the end is not a metacharacter 
[+-*\] # Wrong. here - is the metacharacters, and indicates range between + and *, which is ill-defined. 

TBI In awk, this is fine:  [+\-*\] ⇒ but who can read this now? TBI test this
```

The symbol ```^``` (circumflex or carrot TBI check the spelling of latter) is a metacharacter within ```[ ... ]``` only if it appears at the very first position -- at any other position, it doesn't have any special meaning. Therefore, the regex ```[^...]``` has the following special meaning: ```^``` at the first place excludes all following characters from being matched. For instance:

```bash
$ touch file_A.txt	file_B.txt	file_C.txt	file_D.txt	file_E.txt	file_F.txt	file_G.txt	file_H.txt # or use in Bash brace expansion: touch file_{A..H}.txt
$ ls file_[^ACF].txt
file_B.txt	file_D.txt 	file_E.txt 	file_G.txt 	file_H.txt 
```

Special case: ```]``` as the very first character is just a character (i.e. not a metacharacter, like when it’s on any other place). AB: So, this regex is fine: ```[]]```  ⇒ first ```]``` is a character, second one is metacharacter. TBI 20240224 test all this + check this Special case #4: only in awk, ```\``` escapes any metacharacter, so only on awk this is possible ```[a\]1]```

Sometimes, ```[ ... ]``` is being referred to _bracket expression_ (not to be confused with Bash (TBI check also other shells) _brace expansion_ ```{...}```, which has entirely different meaning (TBI do I really need this here?)

o in awk (and only in awk), \ has to be used within [ ... ] to escape special meaning of charactersv TBI check this + i see this also being necessity in sed + provide example

o TBI how to use [ or ] itself within [ ... ] ? Trivial escaping doesn't work



#### Question mark ```?``` <a name="question.mark"></a>

o the preceding character can appear 0 or one time. And that's it. If it appears more than once, it doesn't match

o "may or may not appear"

o combination

[xy]?

matches if: x and y do not appear. x or y appear.

doesn't match of: both x and y appear. x or y appear 2 or more times.

```bash
$ echo "ac" | egrep "ab?c"
ac
$ echo "abc" | egrep "ab?c"
abc
$ echo "abbc" | egrep "ab?c"
$
$ echo "abcc" | egrep "ab?c"
abcc
```



#### Plus ```+``` <a name="plus"></a>

o the preceding character can appear one or more times but must be present at least once

```bash
$ echo "ac" | egrep "ab+c"
$ echo "abc" | egrep "ab+c"
abc
$ echo "abbc" | egrep "ab+c"
abbc
```
o can be used also in combination with character classes [...]



#### Repetition operator ```\{ ... \}``` and  ```{ ... }``` <a name="repetition"></a>

The notation supported in ERE is ```{ ... }```, while notation ```\{ ... \}```  is used in BRE. For simplicity of notation, here only examples using ERE will be demonstrated, but they remain valid also in BRE, just each curly brace has to be escaped with backslah ```\```. 

o specify limit on repeatable regex - interval

o two formats for the interval:

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

The repetion operator in regex, is not to be confused with brace expansio, TBI finalize and add few examples

o can be used also in combination with character classes [...]

o gawk supports this regex only if flag --re-interval is used



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



#### Grouping operator ```( ... )``` <a name="grouping"></a>

We have already seen that a value can be stored in a variable by explicit assignment (using the operator 



#### POSIX character classes ```[:keyword:]``` <a name="#POSIX.character.classes"></a>

o BRE supports the following (TBI check still for globbing and ERE):

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
* (.*) will match any sequence of characters enclosed in parenthesses





### 4. Further reading <a name="further.reading"></a>

* _"Linux Command Line and Shell Scripting Bible"_, Richard Blum, Christine Bresnahan 
	 * Chapter 20: Regular Expressions
* _"sed & awk"_, Dale Dougherty, Arnold Robbins 
	* Chapter 3: Understanding Regular Expression Syntax
* _"UNIX A history and a Memoir"_, Brian Kernighan
	* Section 4.6: Regular expressions 