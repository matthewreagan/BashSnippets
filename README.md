# Bash Snippets

A curated list of basic Bash scripting snippets and examples.

**Bash Reference Manual**

[https://www.gnu.org/software/bash/manual/html_node/index.html](https://www.gnu.org/software/bash/manual/html_node/index.html)

------

## General

### Default System Shell Shebang
`#!/bin/sh`

(See also: [http://stackoverflow.com/questions/10376206/what-is-the-preferred-bash-shebang](http://stackoverflow.com/questions/10376206/what-is-the-preferred-bash-shebang))

### Simple arithmetic

Use `$(( ))`. See also: [https://bash.cyberciti.biz/guide/Perform_arithmetic_operations](https://bash.cyberciti.biz/guide/Perform_arithmetic_operations)

```
x="3"
y="2"
echo "$(( $x * ( $y + 1 ) ))"
```

## Logic & Control Flow

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

## Files & Paths

### Last file path component

`basename "/My/path/to/file.txt"`
Result: `file.txt`

### Extracting or removing file extension

```
file="Some/path/to/file.txt"
echo "Extension: ${file##*.}"
echo "Name sans extension: ${file%.*}"
```

Result:
```
Extension: txt
Name sans extension: Some/path/to/file
```

### Finding files

Basic matching by name:

`find . -name "*.jpg"`

Combining queries with `-and` / `-or`:

`find . -name "*.swift" -or -name "*.m"`

## Strings

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

## Printing & Stdout

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
