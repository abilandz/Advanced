![](../Common_Figures/LinuxBashROOT_logos.png)


# Regular expressions

**Last update**: 20230920


### Table of Contents
1. [What is a regular expression?](#what.is.regex)
	* [Shell's wildcard expansion or globbing](#globbing)
	* [Basic Regular Expression (BRE)](#bre)
	* [Extended Regular Expression (ERE)](#ere)
	* [Perl-Compatible Regular Expressions (PCRE)](#pcre)
2. [Metacharacters](#metacharacters)
	* [Asterisk](#asterisk) ```*```
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
A regular expression, or _regex_ for short, is a pattern template used to filter text in order to extract the specific information. Or looking from another angle, a regular expression is a pattern that describes a set of strings. Writing a regular expression is equivalent to creating a text filter. Patterns used to define regular expression contain symbols with special, non-literal meaning. In general, such special individual symbols or specific combinations of individual symbols are named _metacharacters_. When interpreted directly by shell to perform filename expansion (or _globbing_), metacharacters are called _wildcards_. To add to the confusion, some metacharacters have different meaning when used in regular expression or in file expansion. Historically, the first implementation and use of reqular expression can be traced back to the late 1960s and Ken Thompson's re-implementation of line-oriented editor QED for Multics (unsuccessful OS which predated Unix). There are four major categories of regular expressions available, and in what follows next, we attempt to systematize.


#### Shell's wildcard expansion or globbing <a name="globbing"></a>
Each shell supports the process of matching expressions containing wildcards to filenames. As the basic example, in the following command input
```bash
ls -al *.txt
```
shell will list all files whose names end with  _.txt_ in the current directory. In the above expression, ```*``` is a _wildcard_ and ```*.txt``` is a _glob_. Basically, glob is a pattern containing one or more wildcards. Its name originates from the early-days Unix command named **glob** (shortcut for 'global'), which was used by shell to expand wildcard characters in the list of file paths, and supply back that list of files to the command. Just like any other frequently used feature, glob mechanism was eventually implemented directly into the shell.

The most frequent wildcards are: ```?``` ```*``` ```[]``` . When these special symbols are used in regular expressions, their interpretation can be different. Therefore, in the next section we enlist systematically all special characters, and explain their use cases separately in regular expressions and in filename expansion, whenever it differs.






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
* programs which support it: **ed**, **sed**, and **grep**


#### Extended Regular Expression (ERE) <a name="ere"></a>

* standardized by POSIX, a few additional metacharacters when compared to BRE
* programs which support it: **egrep** (or **grep -E**), **awk**, **sed -E**, operator =~ in recent versions of **Bash**

#### Perl-Compatible Regular Expressions (PCRE) <a name="pcre"></a>

* standalone library written in C (  [https://www.pcre.org/](https://www.pcre.org/) ), the most powerful implementation of regex, aimed initially to provide all regex features available in **perl** programming language 
* use cases are rather limited for regular daily tasks, therefore not covered here in detail
* PCRE syntax for regular expressions are supported in: **perl** (obviously), **grep -P**


In what follows next, we enlist and briefly discuss all metacharacters which appear in globbing, BRE and ERE.


### 2. Metacharacters <a name="metacharacters"></a>

Metacharacter is a symbol, or combination of symbols, with special and non-literal meaning in regular expressions and filename expansions. Despite its peculiar name, metacharacters are present all around us. For instance, in math we are used to using metacharacters, for instance in the arithmetic expression ```4 * 10``` we understand that metacharacter ```*``` represents multiplication. As another example, combination of symbols ```\n``` is a composite metacharacter, and is common metacharacter for new line.



#### Asterisk ```*``` <a name="asterisk"></a>
1. In BRE will match any number of repetitions of the preceeding character or, in a more elaborate case, it will match zero or more occurrences of the preceding regular expression. By itself, asterisk “*” matches nothing, it modifies what goes before it 

2. As a shell wildcard, meaning of * is completely different, there it means "zero or more characters"  

Example: Regular expression for "any number of characters (including 0 characters!!)" is .*

Example: Regex A.*E matches ACE, AIRPLANE, A LONG WAY HOME, etc., but also it matches AE


- When it’s applied on a single character, that character may be there or not, and if it is, there may be more than one of them
    - `[ab]c*` — matches “ab”, “abc”, “abcc” **(TBI this is my example, test it)**
    - `[ab]cc*` — matches “abc”, “abcc”, “abccc”, but not “ab” **(TBI this is my example, test it)**
- Classical examples:
    - `*` — matches one or more spaces
    - `.*` — matches any number of characters
    - `".*"` — matches any string within quotes. The span matched by it is always longest possible
    - `grep '<.*>' someHtml` — matches any formatting instruction in html
    - `[no]*` — in combination with character classes, it matches any number of characters in that class, but also in any ordeer. So this would match no, nno, noo, , on, oon, onn, etc.  **(TBI test it)**
- Closure ⇒ the ability to match “zero or more” of something


#### Dot ```.``` <a name="dot"></a>
In BRE the dot ```.``` will match any single character, except newline. It can be thought of as a sort of "variable" in regex, in analogy with the case where variable represents any value in arithmetic expression. (TBI it seems that in awk dot matches also embedded new line => check)

Example #1: Demonstrate that regex "Chapter." matches Chapter anywhere, except at the end of the line, because `.` doesn’t match end of line (TBI finalize this example)


#### Character classes ```[ ... ]``` <a name="character.classes"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### Question mark ```?``` <a name="question.mark"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### Plus ```+``` <a name="plus"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### Repetition operator ```\{ ... \}``` and  ```{ ... }``` <a name="repetition"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### Alternation operator ```|``` <a name="alternation"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### Grouping operator ```( ... )``` <a name="grouping"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


#### POSIX character classes ```[:keyword:]``` <a name="#POSIX.character.classes"></a>
We have already seen that a value can be stored in a variable by explicit assignment (using the operator 


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