# Bash Snippets

A curated list of basic Bash scripting snippets and examples.

### Default System Shell Shebang
`#!/bin/sh`

### Looping over a range
```
for i in {1..5}; do echo $i; done
```

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

### Script arguments

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

### Bash functions

```
sayHello() {
 printf "Hello"
}

sayHello
```

### Directing command stdout and stderr to /dev/null
`echo "silence" &> /dev/null`

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

### Logarithmic values via bc
`echo 'l(100)/l(10)' | bc -l`

[http://stackoverflow.com/questions/7962283/how-do-i-calculate-the-log-of-a-number-using-bc](http://stackoverflow.com/questions/7962283/how-do-i-calculate-the-log-of-a-number-using-bc)
