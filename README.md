# Bash Snippets

A curated list of basic Bash scripting snippets and examples. See also: [Bash Reference Manual](https://www.gnu.org/software/bash/manual/html_node/index.html)

### Contents

- [Simple Arithmetic](#simple-arithmetic)
- [Logic & Control Flow](#logic-control-flow)
- [Files & Paths](#files-paths)
- [Image Files](#image-files)
- [Strings](#strings)
- [Pattern Matching / Regex](#pattern-matching-regex)
- [Script Arguments](#script-arguments)
- [Interactive console](#interactive)
- [Functions](#functions)
- [Piping & Command Substitution](#piping-command-substitution)
- [Printing & Stdout](#printing-stdout)
- [Math](#math)
- [HTTP / Network](#http-network)
- [Compression](#compression)
- [git](#git)

------

## General

### Default System Shell Shebang
`#!/bin/sh`

(See also: [http://stackoverflow.com/questions/10376206/what-is-the-preferred-bash-shebang](http://stackoverflow.com/questions/10376206/what-is-the-preferred-bash-shebang))

## Simple arithmetic

Use `$(( ))`. See also: [https://bash.cyberciti.biz/guide/Perform_arithmetic_operations](https://bash.cyberciti.biz/guide/Perform_arithmetic_operations)

```
x="3"
y="2"
echo "$(( $x * ( $y + 1 ) ))"
```

## Logic, Control Flow

### If-Then-Else Examples

```
a="1"
b="2"

if [ $a -lt $b ]; then
 echo "$a is less than $b."
else
 echo "$a is greater than or equal to $b."
fi

if (( $a > $b ))
then
 echo "$a is greater than $b."
else
 echo "$a is less than or equal to $b."
fi
```

### Looping over a range
```
for i in {1..5}; do echo $i; done
```

### Looping over pattern-matched files

```
for i in *.mp3; do echo $i; done
```
```
for i in `find . -name "*.JPG"`; do echo $i; done
```

### Multi-line looping example

```
for f in ~/Downloads/*.JPG
do
 echo "Full path: $f"
 echo "Just the name: $(basename $f)"
done
```

### Basic comparators
List of comparison operators: [http://tldp.org/LDP/abs/html/comparison-ops.html](http://tldp.org/LDP/abs/html/comparison-ops.html)

```
aNumber="2"
if [ $aNumber -lt 3 ]
then
 echo "$aNumber is less than 3"
else
 echo "$aNumber is greater than or equal to 3"
fi
```

### Basic comparators (double paranthesis form)

```
aNumber="4"
if (($aNumber < 3))
then
 echo "$aNumber is less than 3"
else
 echo "$aNumber is greater than or equal to 3"
fi
```

### While loop

```
a=0
b=5

while (( $a < $b ))
do
 echo $a
 a=$(( a + 1 ))
done
```

## Files, Paths

### Last file path component

`basename "/My/path/to/file.txt"`
Result: `file.txt`

### Parent directory

`dirname "/My/path/to/file.txt"`
Result: `/My/path/to`

### Extracting, removing, or replacing file extension

```
file="Some/path/to/file.txt"
echo "Extension: ${file##*.}"
echo "Name sans extension: ${file%.*}"
echo "New extension: ${file%txt}gif"
```

Result:
```
Extension: txt
Name sans extension: Some/path/to/file
New extension: Some/path/to/file.gif
```

### Finding files

Basic matching by name:

`find . -name "*.jpg"`

Combining queries with `-and` / `-or`:

`find . -name "*.swift" -or -name "*.m"`

### Finding files + coping with spaces for xargs

Won't work if paths contain spaces etc.:

~~`find . -name "*.swift" | xargs wc -l`~~ 

Works:

`find . -name "*.swift" | sed 's/ /\\ /g' |  xargs wc -l`

Also works:

`find . -name "*.swift" -print0 | xargs -0 wc -l`

See also: [https://stackoverflow.com/questions/16758525/how-can-i-make-xargs-handle-filenames-that-contain-spaces](https://stackoverflow.com/questions/16758525/how-can-i-make-xargs-handle-filenames-that-contain-spaces)

## Image Files

### Batch-converting images with `sips`

```
for i in `find . -name "IMG_04*.HEIC"`; do sips -s format jpeg -s formatOptions 70 "${i}" --out "${i%HEIC}JPG"; done
```

## Strings

### String comparison

```
if [ "$1" == "hi" ]; then
  echo "First arg is 'hi'!"
fi
```

### String concatenation

```
a="Hello"
b="world"

echo $a", "$b"!"
echo "$a, $b!"
echo "${a}, ${b}!"

str=$a
str+=", "
str+=$b
str+="!"
echo $str
```

### String adjustments

Components via **cut**

```
echo "Hello world and good day." | cut -d " " -f 1    #Result: Hello
echo "Hello-world-and-good-day." | cut -d "-" -f 2    #Result: world
```

Character replacement

```
echo "Hello" | tr 'el' 'x'    #Result: Hxxxo
echo "Hello" | tr 'el' 'ay'   #Result: Hayyo
```


Uppercasing / lowercasing

```
echo "Hello" | tr '[:lower:]' '[:upper:]'   #Result: HELLO
echo "Hello" | tr '[:upper:]' '[:lower:]'   #Result: hello
```


Removing characters

`echo "Hello" | tr -d "el"    #Result: Ho`


Substituion via **sed**:

`echo "The quick brown fox" | sed 's/brown/red/'    #Result: The quick red fox`

_Note that sed, by default, matches once per line and is case sensitive:_

`echo "Hello, hello, hello" | sed 's/hello/goodbye/'    #Result: Hello, goodbye, hello`


Pattern-matched replacement:

```
echo "abc123def456" | sed -E 's/[a-z]*/First letters ==> & <== /'
Result: First letters ==> abc <== 123def456
```

See also: [Sed Introduction and Tutorial](http://www.grymoire.com/Unix/Sed.html)

See also: [RegEx Cheat Sheet](http://www.rexegg.com/regex-quickstart.html)

## Pattern Matching, RegEx 

Below are several pattern matching examples which build progressively upon each other. Before this, however, the below `sed` syntax deserves a special mention due to its versatility. By far the most common pattern matching task I encounter is to match a pattern and extract a portion. There are a variety of ways to do this, however the `sed` command below is quite handy:

`sed -n "s| <regex> \( <regex> \) |\1|p"`

The above command can be used to match any pattern and extract some or all of the text as the output.

- The `-n` flag suppresses sed's default behavior to print every input line
- The `s` flag indicates a substitution operation
- The first block of text is the pattern to match, and we also specify a capture group via the escaped parantheses `\(` and `\)`
- The `p` flag instructs sed to print only the text which is substituted
- The `\1` for the substitution will match the text in our capture group (the parentheses)

**Examples**:

`echo "The quick brown fox jumps over the lazy dog." | sed -n "s|.*the \(.*\) dog.*|\1|p"`

Result: `lazy`

Comment: note the `.*` at the very beginning and end of the pattern, which captures all of text before or after the matched portion. This ensures that nothing except the match will be part of the substituion & output.

---

`echo "The quick brown fox jumps over the lazy dog." | sed -n "s|.*\(the .* dog\).*|\1|p"`

Result: `the lazy dog`

---

`echo "The quick brown fox jumps over the lazy dog." | sed -n "s|.*the \(.*\) dog.*|What is the dog? \1|p"`

Result: `What is the dog? lazy`

---

`echo "The quick brown fox jumps over the lazy dog." | sed -n "s|the \(.*\) dog|something else|p"`

Result: `The quick brown fox jumps over something else.`

---

### More Pattern Matching

**Input text**:
```
<HTML>
	<HEAD>
		<script>
			myFunc1('one');
			myFunc2('two');
			myFunc3('three');myFunc4('four');
		</script>
	</HEAD>
	The quick brown fox jumped over the lazy IBM.
</HTML>
```

---

**Command**:
`grep myFunc`

**Output**:
```
			myFunc1('one');
			myFunc2('two');
			myFunc3('three');myFunc4('four');
```   

**Command**:
`grep myFunc -o`

**Output**:
```
myFunc
myFunc
myFunc
myFunc
```   

**Command**:
`grep -E myFunc.* -o`

**Output**:
```
myFunc1('one');
myFunc2('two');
myFunc3('three');myFunc4('four');
```

*Discussion:* Note the greedy matching of myFunc3 + myFunc4, this is discussed further below.

**Command**:
`sed -n "s|.*myFunc1('\(.*\)');.*|\1|p"`

**Output**:
```
one
```

*Discussion:* The `-n` option supresses / silences sed's default output of every line. The `p` flag in the regex prints just the text which was substituted. The `\1` substitution specifies the first matching group from the pattern. The group is the text contained within the set of escaped parentheses `\(` & `\)`. So this regex matches the entirety of any line which contains `myFunc('…')`, and prints out just the matching group value.


**Command**:
`sed -n "s|.*\(myFunc[0-9]\)('\(.*\)');.*|\1 === \2|p"`

**Output**:
```
myFunc1 === one
myFunc2 === two
myFunc4 === four
```

*Discussion:* Builds on previous example by matching any `myFunc[N]`, and including that first match in a group. The output is changed to `\1 === \2` to print both groups.

*Note on greedy matching*: POSIX regex doesn't support lazy / non-greedy captures (`.*?`), which is why `myFunc3` is omitted (the `.*` captures it as part of the match). See below.

**Command**:
`grep -oE "myFunc[0-9]\('.*?'\);"`

**Output**:
```
myFunc1('one');
myFunc2('two');
myFunc3('three');
myFunc4('four');
```

*Discussion*: We make use of a lazy / non-greedy capture here (`.*?`, instead of `.*`) with `grep` to match against `myFunc3` and `myFunc4` separately. The `-o` flag prints 'only' the match, and the `-E` specifies we're using an expression. Note that unlike `sed`, the parentheses must be escaped when we're attempting to match them explicitly (as opposed to escaping them to _avoid_ matching them.)

**Command**:
`grep -oE "myFunc[0-9]\('.*?'\);" | sed -n "s|\(myFunc[0-9]\)('\(.*\)');|\1 === \2|p"`

**Output**:
```
myFunc1 === one
myFunc2 === two
myFunc3 === three
myFunc4 === four
```

*Discussion*: Contrived example, fixes the greedy matching in `sed` by first matching with the non-greedy `grep` example further up. This gives us a exhaustive match across each line.

**Command**:
`sed "s|myFunc|theirFunc|"`

**Output**:
```
<HTML>
	<HEAD>
		<script>
			theirFunc1('one');
			theirFunc2('two');
			theirFunc3('three');myFunc4('four');
		</script>
	</HEAD>
	The quick brown fox jumped over the lazy IBM.
</HTML>
```

**Command**:
`sed "s|myFunc|theirFunc|g"`

**Output**:
```
<HTML>
	<HEAD>
		<script>
			theirFunc1('one');
			theirFunc2('two');
			theirFunc3('three');theirFunc4('four');
		</script>
	</HEAD>
	The quick brown fox jumped over the lazy IBM.
</HTML>
```


## Script Arguments

### Script arguments

Available via `$1`, `$2`, etc.

```
echo "Hello, '$1 $2'"
```

```
./myScript.sh Bob Smith
 Hello, 'Bob Smith'
```

### Checking argument count

Available via `$#`

```
if [ $# -ne 2 ]
then
 echo "Expecting 2 arguments"
 exit 1
fi
```

### Current script path

Available via `$0`

### Exit status of most recent command

Available via `$?`

```
cp /some/invalid/path another/invalid/path
if [ $? -eq 0 ]
then
 echo "Copied successfully."
else
 echo "Error: non-zero exit code."
fi
```

## Interactive

### Reading input

```
echo "Hi, what is your name?"
read response
if [ -n "$response" ]; then
 username=$response
else
 echo "Don't feel like talking, huh?"
 exit 0
fi

echo "Do you like Apple gadgets, $username? (y/n)"
read response
if [ "$response" != "y" ]; then
 echo "That makes me sad."
else
 echo "Me too!"
fi
```

## Functions

### Bash functions

```
sayHello() {
 printf "Hello"
}

sayHello
```

### Bash functions with arguments

```
sayHelloToPerson() {
 printf "Hello $1"
}

sayHelloToPerson Matt
```

### Bash functions which 'return' a value

```
giveMeAString() {
 echo "Some result"
}

result=$(giveMeAString)
echo $result
```

## Piping, Command Substitution

### Capturing output of another shell command

Backticks:
```
str="a\nb\nc"
result=`echo $str | grep "b"`
echo $result
```

or $():
```
str="a\nb\nc"
result=$(echo $str | grep "b")
echo $result
```

Piping one value to multiple commands

`echo "Hi"| tee >(xargs echo) >(xargs echo) | xargs echo`

_Note: This trick is not compatible with some shells / environments. See also: [this post](https://unix.stackexchange.com/questions/28503/how-can-i-send-stdout-to-multiple-commands)._

## Printing, Stdout

### (Re)printing on the same line
Use `\r`

### (Re)printing on the same line without leftover characters
Useful for progress bars or printing multiple outputs on the same line during the script's running process. The example code below which leverages `tput` avoids the leftover characters of the previously printed line.

```
tput sc #save cursor

tput rc;tput el
printf "Line 1 which is longer than the next line\r"
sleep 1
tput rc;tput el
printf "Follow up line will overwrite"
```

### Directing command stdout and stderr to /dev/null
`echo "silence" &> /dev/null`

## Math

### Logarithmic values via bc
`echo 'l(100)/l(10)' | bc -l`

[http://stackoverflow.com/questions/7962283/how-do-i-calculate-the-log-of-a-number-using-bc](http://stackoverflow.com/questions/7962283/how-do-i-calculate-the-log-of-a-number-using-bc)

## HTTP, Network

### Pretty JSON
To pretty-format JSON, pipe it into `python` using _json.tool_. Example:

`cat myJSONFile.json | python -m json.tool`

### curl
`curl` is the go-to utility for testing network endpoints, server responses, crafting forms, submitting POST requests, downloading files, and more: [curl Manual](https://curl.haxx.se/docs/manual.html) | [curl Cheatsheet](https://devhints.io/curl)

### Testing time-to-first-byte
`curl -w "Connect time: %{time_connect} Time to first byte: %{time_starttransfer} Total: %{time_total} \n" "http://example.com/1/endpoint" -s -o /dev/null`

See also: [Related article](http://sound-of-silence.com/?page=blog&article=20160404)

### Download files using glob pattern
`curl "http://somewebsite.com/files[0001-0010].txt" -o "file_#1.txt"`

## Compression

### Create password-protected ZIP archive

`zip -er myArchive.zip FolderName`

The `-r` flag provides recursion (zipping a folder). The password for the encryption by default will be entered at a prompt after the command is run.

### Decompress Zip

`unzip myArchive.zip`

## git

### Find & checkout branch by partial name

Sample script snippet. Also demonstrates use of terminal colors etc.

```
#!/bin/sh
green='\033[0;35m'
nocolor='\033[0m'
branch=`git branch | grep $1 -m 1`

if [ "$branch" = "" ]; then
	echo "No branch found containing '$1'."
	exit 0
fi

echo "Check out: ${green}$branch${nocolor}"
echo "(y/n)"
read response
if [ "$response" != "y" ]; then
	echo "Aborted."
else
	git checkout $branch
fi
```

### Useful Git CLI Utilities

This repo contains a number of useful git commands and utilitiy scripts:

[https://github.com/nvie/git-toolbelt](https://github.com/nvie/git-toolbelt)
