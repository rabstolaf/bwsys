# Bash Scripting Lesson
This document will cover the basic features of bash scripting
There times where I reference the following link, as it is very comprehensive
#### [Bash Reference Manual](https://nodesource.com/products/nsolid)

## Introduction
Bash is a very common shell interface on linux machines. Bash scripts are often used to take make repetative tasks simple, or automate a process.
Any line begining with a $ will be a command line command. 

## Basics
To create your first script,
```sh
$ touch myscript.sh
```
This will create an empty file named _myscript.sh_
Notice the .sh file extension. This is not requrired but it is convention. 

Let's edit this file to create our first script. 
```sh
$ nano myscript.sh
```
Here we will write the script
```sh
#!/bin/bash
# A simple bash script
echo Hello World!
```
The first line will be repeated through all bash scripts. The first two characters (#!) are known as the Shebang. They set the path to the interpreter, in our case, the bash shell. 

Pound symbols are used to denote a comment in bash scripting

The third line is the command we will execute. Notice that you can also type this command in the terminal and you will get the same result. 

Lets run this script. From the command line, run the program
```sh
$ ./myscript.sh
```
This should have returned 
```sh
bash: ./myscript.sh: Permission denied
```
This is because by default, files are not executable, a simple fix.
```sh
$ chmod u+x myscript.sh
```
This will add (+) execute (x) permission to the user (u)
When you run the program this time, you should get an output. 
```sh
$ ./myscript.sh
Hello World!
```

## Variables
Before we get into variables in scripting, lets try out variables on the command line first. 
```sh
$ A=apple
$ echo $A
apple
```
These lines set A to the string apple, keep in mind that you canno't have any spaces. 
To read the variable, put a dollar sign in front of its name. 
Bash has many special variables with information about the enviroment, called enviroment variables, they can be accessed the same as variables you set manualy, for example,
```sh
$ echo $HOME
/home/user
```
There are also special variables that can be accessed in side the script itself

| Variable | Value |
| ------ | ------ |
| $0 | Name of script |
| $1,$2,...,$9 | Script Arguments 1 - 9 |
|  $# | Number of arguments |
| $USER | Name of user running the script |
| $HOSTNAME | Machine hostname|
| $RANDOM | Returns a random number  |

More variables can be found in the [Reference Manual](https://nodesource.com/products/nsolid)

These are some of the basic variables

One more thing to watch out for is variable scope, much like in other programming languags. A variable created in the script will not be avalible on the command line afterward unless you expicitly say so. This is done using the _export_ command.
To test this, in a new script, try exporting a variable
```sh
#!/bin/bash
var1=foo
var2=bar

export var1
```
After you run this program, try to find out what the variables are set to on the command line.
```sh
$ echo var1

$ echo var2
```

You can also save the output of a command directly into a variable
```sh
#!/bin/bash
var=$(ls)
echo $var
```
This saves the return from ls into our variable for later use.
## Arithmetic Expressions
#### Let
the _let_ command allows you to compute simple arithmetic expressions

```sh
#!/bin/bash

let a=1+1
echo $a # 2

let "a = 1 + 1"
echo $a # 2

let a++
echo $a # 3
```
#### Expr
This is similar to _let_ except, it prints the result instead of saving it to a variable
```sh
#!/bin/bash

expr 3+4 

expr 15 % 2
```
When we run this, we get
```sh
$ ./exprExample.sh
7
1
```
#### Double parentheses
$(( expression ))
This mechanism allows you to easily manipulate the value of a variable, similar to the let command.
```sh
#!/bin/bash

var=$(( 2+5 ))
echo $var # 7

var=$((1+1)) # spacing does not matter in this format
echo $var # 2

(( var++ ))
echo $var # 3
```
## Input
In bash scripting you can also get input from the user
```sh
#!/bin/bash
echo Please enter some input:
read var
echo you entered $var
```

## Control structures
In this section we will get into options for control structures in bash scripting
### If statements

This example will take a few things we have been over so far and combine them
```sh
#!/bin/bash
if [$1 -qt 100 ]
then
    echo You entered a number larger than 100
fi

if (($1 == 0))
then
    echo Your number is 0
fi

```
There are many test cases you can put in the square brackets ( _[ ]_ ). Here are a few examples

| Operator | Test |
| ------ | ------ |
| -n STR | String is longer than zero characters |
| -z STR | String length is zero |
| -d DIR | Directory exists |
| -e FILE| File exists |
[Bash Reference Manual](https://nodesource.com/products/nsolid)
You can see that both of the if statements end their scope with _fi_

Else if and Else also can be acomplished by adding
```sh
if [ condition ]
then
    commands
elif [ condition ]
then
    commands
else
    commands
fi
```
in between _if_ and _fi_ 

### Case statements
```sh
#!/bin/bash

case $1 in
    start)
        echo starting
        ;;
    stop)
        echo stoping
        ;;
    restart)
        echo restarting
        ;;
    *)
        echo command not found
        ;;
esac
```
This simple case statement uses the first command line argument as its conditional.
If _$1_ is equal to the string start, it will echo starting
```sh
$ ./caseExample.sh start
starting
```
If the variable is not set to one of our options, the ' _*_ ' option will handle anything.
```sh
$ ./caseEcample.sh test
command not found
```

### While statements
While statement structure.
```sh
while [ a test ]
do
    commands
done
```
Sample implamentation
```sh
#!/bin/bash
i=0
while[i -le 10]
do
    echo $i
    ((i++))
done
echo Done
```
This will print
```sh
$ ./whileExample.sh
1
2
3
4
5
6
7
8
9
10
Done
```
### Until
This is the opposite of a _while_ loop. In an Until loop, it executes the do section until the test becomes true.
### For statements
For loops in Bash scripting are similar to how they are written in python
```sh
for VAR in LIST
do
    COMMANDS
done
```

```sh
#!/bin/bash

for var in {0..5} # Creates an range of integers 0 to 5
do
    echo $var
done
echo Done
```

```sh
$ ./forExample.sh
0
1
2
3
4
5
Done
```
### Break
The break keyword leaves the loop immediately
### Continue
The continue keyword will skip the rest of that iteration of the loop and move on to the next one

## More advanced Bash Scripting
### Functions
function delcarations come in two different forms
```sh
func1 () {
    commands
}

function func2 {
    commands
}
```
Functions in bash scripting act in some ways like new processes. Because of this, if you want to pass arguments to a bash function, you need to use the _$1_ ... _$n_ variables

```sh
#!/bin/bash

print_a_thing () {
    echo $1
}

print_a_thing foo
print_a_thing bar
```
```sh
$ ./funcExample.sh
foo
bar
```
You can also use return values
```sh
#!/bin/bash

just_a_function () {
    echo This is a function
    return 3
}

just_a_function
echo The function returned $?
```
```sh
$ ./functionReturn.sh
This is a function
3
```
The value of the most recent funtion's return value is stored in the variable '?' and can be accessed with '$?'
