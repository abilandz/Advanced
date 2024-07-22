![](../Common_Figures/LinuxBashROOT_logos.png)


# Regular expressions

**Last update**: 20240722


### Table of Contents
1. [What is a regular expression?](#what.is.regex)
	
	* [Shell's wildcard expansion in filenames (globbing)](#globbing)
	* [Basic Regular Expression (BRE)](#bre)
	* [Extended Regular Expression (ERE)](#ere)
	* [Perl-Compatible Regular Expressions (PCRE)](#pcre)
	
2. [Metacharacters](#metacharacters)
	
	* [Dot](#dot)  ```.```
	* [Asterisk](#asterisk) ```*```
	* [Backslash](#backslash)  ```
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
A regular expression, or _regex_ for short, is a pattern template used to filter text in order to extract specific information. Or, looking from another angle, a regular expression is a pattern that describes a set of strings. Writing a regular expression is equivalent to creating a text filter. Patterns used to define regular expression contain symbols with special, non-literal meaning. In general, such special individual symbols or specific combinations of individual symbols, are named _metacharacters_. When interpreted directly by a shell to perform filename expansion (or _globbing_), metacharacters are called _wildcards_. To add to the confusion, some metacharacters have different meanings when used in regular expression or in filename expansion. 

Historically, the first implementation and use of regular expression can be traced back to the late 1960s and Ken Thompson's re-implementation of line-oriented editor QED for Multics (an unsuccessful operating system that predated Unix). There are four major categories of regular expressions, and in what follows next, we attempt to systematize.


#### Shell's wildcard expansion in filenames (globbing) <a name="globbing"></a>
Each shell supports the process of matching expressions containing wildcards to filenames. In fact, one of the most important features of shell is to be able to operate on multiple files simultaneously. As the basic example, in the following command input
```bash
ls -al *.txt
```
shell will list all files whose names end with _.txt_ in the current working directory. In the above expression, ```*``` is a _wildcard_ and ```*.txt``` is a _glob_. Basically, _glob_ is a pattern containing one or more wildcards. Its name originates from the early-days Unix command named **glob** (shortcut for 'global'), which was used by the shell to expand wildcard characters in the list of file paths, and supply back that list of files to the command. Just like any other frequently used feature, this mechanism was eventually implemented directly into the shell.

The standard and most frequent shell's wildcards used in filename expansions are: ```?``` ```*``` ```[]``` ```[!]``` ```{}``` ```\``` 

It is important to remember that when these special shell symbols are used in regular expressions, their interpretation can be different. In addition, when specifying regex as a pattern in commands like **grep**, **find**, etc., that regex always needs to be embedded within strong quotes, schematically as ```'some-regex-with-special-symbols'```, so that these special symbols are not interpreted and expanded first by the shell as its wildcards, before supplying that regex to the command.


#### Basic Regular Expression (BRE) <a name="bre"></a>

* standardized by POSIX
* metacharacters: ```.``` ```*``` ```[]``` ```^``` ```$``` ```\``` and compound repetition operator ```\{n,m\}```  
* example programs that support BRE syntax: **ed**, **sed**, and **grep**


#### Extended Regular Expression (ERE) <a name="ere"></a>

* standardized by POSIX
* all metacharacters supported by BRE, only with a slightly different notation for compound repetition operator ```{n,m}```
* additional metacharacters when compared to BRE: ```+``` ```?``` ```|``` ```()``` 
* first implemented by Alfred Aho in 1979 in the extended version of __grep__ called __egrep__ 
* example programs that support ERE syntax: **egrep** (or **grep -E**), **awk**, **sed -E**, operator **=~** (only in recent versions of **Bash** where it can be used within ```[[ ... ]]``` environment)

#### Perl-Compatible Regular Expressions (PCRE) <a name="pcre"></a>

* standalone library written in C ( [https://www.pcre.org/](https://www.pcre.org/) ), the most powerful implementation of regex, aimed initially to provide all regex features available in the **perl** programming language
* use cases are rather limited in custom daily tasks, therefore not covered here in detail
* PCRE syntax for regular expressions is supported, for instance, in: **perl** (obviously), **grep -P**, etc.

In the next section, we systematically enlist all metacharacters and explain their use cases when they appear as shell wildcards (i.e. in globbing), in BRE, or in ERE, using for testing **Bash**, **grep** or **egrep**, respectively.






### 2. Metacharacters <a name="metacharacters"></a>

Metacharacter is a symbol, or combination of symbols, with special and non-literal meaning in regular expressions and filename expansions. Despite its peculiar name, metacharacters are present all around us. In math, we are used to using metacharacters; for instance, in the arithmetic expression ```4 * 10```, we understand that metacharacter ```*``` represents multiplication. As another example, a combination of symbols ```\n``` is a composite metacharacter and is a common metacharacter for a new line.





#### Dot ```.``` <a name="dot"></a>
The metacharacter dot ```.``` has the special meaning only in BRE and ERE. The reason why it doesn't have any special meaning as a wildcard in globbing is originating from the fact that ```.``` as a literal character is already used heavily to denote hidden files (the files whose names begin with ```.``` , like in ".bashrc", and which are not listed by default with the __ls__ command), and to separate filename from file extension that identifies the file format (e.g. "someFile.txt" for ASCII file). 

In regex the dot ```.``` will match any single character, except newline. The character must be present (zero occurrences do not count), and space also counts as a character. It can be thought of as a sort of "variable" in regex, in analogy with the case when a variable represents any value in an arithmetic expression. One can also say that dot ```.``` specifies a position that any character can fill. In globbing, the wildcard ```?``` has similar meaning (see below).

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

$ grep \. someFile # WRONG!! Shell already interpreted \ and what grep sees is the metacharacter .
2.44
244

$ grep \\. someFile # OK, shell didn't interpret \ because it's escaped
2.44
```



To do: 20240722

Example #1: Demonstrate that regex "Chapter." matches Chapter anywhere, except at the end of the line, because `.` doesn’t match end of line (TBI finalize this example)

Exceptions:

1. (TBI it seems that in awk dot matches also embedded new line => check



#### Asterisk ```*``` <a name="asterisk"></a>
1. In BRE and ERE, it will match any number (including zero) of repetitions of the preceding character or, in a more elaborate case, it will match zero or more occurrences of the preceding regular expression. By itself, the asterisk ```*``` matches nothing, it modifies what goes before it;

2. As a shell wildcard, the meaning of the asterisk ```*``` is completely different &mdash; in this context, it stands for "zero or more characters."

__Example 1__: Regex for "any number of characters (including 0 characters!)" is ```.*```

__Example 2__: Regex ```A.*E``` matches ACE, AIRPLANE, A LONG WAY HOME, etc., but also it matches AE


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

To do for this section:

20240721 It's still a mess



#### Backslash ```\``` <a name="backslash"></a>
The backslash metacharacter ```\``` is typically used in both directions:
1. It turns another metacharacter into an ordinary character (standard escape mechanism), as in:
```bash
$ Var=44
$ echo $Var
44
$ echo \$Var
$Var
```

​       In this context, a frequent use case is ```\\```, which searches for literal ```\```. 	

2. It turns the ordinary characters into compound metacharacter (```\n``` is the standard compound metacharacter for a new line, ```\t``` for tab spacing, etc.).

TBI do I comment on its meaning in globbing?

TBI AB: If you need a literal search for metacharacter containing already `\\` , you have to escape it, like in `\\\\n` **TBI test this**

only sed: These are metacharacters: `\\(` `\\)` `\\{` `\\}` `\\N (in this context, N stands for a digit from 1 to 9)`



Remark: Slash ```/``` is not a special character but nevertheless in sed and gawk needs to be escaped, due to conflict with internal syntax





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
$ echo ayc | grep "a[bx]c" # doesn't match, "y" is not enlisted between "[" and "]"
```


A very handy use case is to tolerate common misspellings when looking for a particular pattern:

maint[ea]n[ea]nce
l[au]nch

TBI build a concrete example for 2 above

Another common use case is to ignore in the search if the word was at the begininng of the sentence, and therefore capitalized, or not.
```bash
$ echo someWord | grep "[sS]omeWord"
someWord
$ echo SomeWord | grep "[sS]omeWord"
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

The symbol ```^``` (_circumflex_ or _caret_) is a metacharacter within ```[ ... ]``` only if it appears at the very first position &mdash; at any other position, it doesn't have any special meaning. Therefore, the regex ```[^...]``` has the following special meaning: ```^``` in the first place excludes all following characters from being matched. For instance:

```bash
$ touch file_A.txt	file_B.txt	file_C.txt	file_D.txt	file_E.txt	file_F.txt	file_G.txt	file_H.txt # or use in Bash brace expansion: touch file_{A..H}.txt
$ ls file_[^ACF].txt
file_B.txt	file_D.txt 	file_E.txt 	file_G.txt 	file_H.txt 
```

Only in globbing, the synonym for ```[^...]``` is ```[!...]```. But because the notation ```[!...]``` does not have any special meaning in BRE or in ERE, and to avoid confusion, we recommend the usage only of  metacharacter ```[^...]``` in any context.

Special case: ```]``` as the first character is just a character (i.e. not a metacharacter, like when it’s on any other place). AB: So, this regex is fine: ```[]]```  ⇒ first ```]``` is a character, second one is metacharacter. TBI 20240224 test all this + check this Special case #4: only in awk, ```\``` escapes any metacharacter, so only on awk this is possible ```[a\]1]```

Sometimes, ```[ ... ]``` is being referred to _bracket expression_ (not to be confused with Bash (TBI check also other shells) _brace expansion_ ```{...}```, which has entirely different meaning (TBI do I really need this here?)

o in awk (and only in awk), \ has to be used within [ ... ] to escape special meaning of charactersv TBI check this + i see this also being necessity in sed + provide example

o TBI how to use [ or ] itself within [ ... ] ? Trivial escaping doesn't work



#### Question mark ```?``` <a name="question.mark"></a>

1. Shell wildcard:  any single character



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



`?` — matches zero or one occurrences of the preceeding regex

- `80[234]?86` would match 80286, 80386, 80486, but also 8086,

- Important: Shell’s wildcard ? has different meaning — it stands for single character, like 

  ```
  .
  ```

   in regex

  - `?` wildcard in shell — `.` in regex





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

`+` — matches one or more occurrences of the preceeding regex (”at least one”)

- ```   + ``` or  ```   * ``` — matches one or more spaces TBI check this



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
   * https://man7.org/linux/man-pages/man7/glob.7.html ( or execute locally: ```$ man 7 glob``` )
   * https://man7.org/linux/man-pages/man7/regex.7.html  ( or execute locally: ```$ man 7 regex``` )
* Online regex checker: https://regex101.com/