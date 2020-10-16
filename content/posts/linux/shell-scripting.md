---
title: "Shell Scripting"
date: 2020-08-06T21:33:42+08:00
draft: false
toc: true
mathjax: false
tags:
  - linux
---

## 1. Basic Scripting Building

### 1.1 Displaying Messages

Use `echo` to display messages.

```bash
echo "Hello world!"
```

### 1.2 Using Variables

Define and reference a variable.

```bash
val=10
echo $val
```

Command substitution.

```bash
val1=`ls`
val2=$(pwd)
```

### 1.3 Redirecting Input and Output

Output redirection.

```bash
# overwriting the file
command > outputfile
# appending output to the file
command >> outputfile
```

Input redirection

```bash
command < inputfile
# inline input redirection
command << marker
data
marker
```

### 1.4 Pipes

```bash
command1 | command2
```

### 1.5 Performing Math

The bash shell mathematical operators support only integer.

```bash
# the value of val would be 1
val=$[5/4]
```

Use external command, such as Python,
to support full floating-point arithmetic.

```bash
val=$(python << EOF
import math
area=math.pi * (2 ** 2)
print(area)
EOF
)
```

### 1.6 Existing the Script

Use `echo $?` to check the exit status.
Use `exit N` to return the status `N`.

## 2. Conditional Statements

### 2.1 Using if-then Statements

There are two forms of the if-then statement.
The second one is more popular.

```bash
# form 1
if command
then
    commands
else
    commands
fi

# form 2
if command; then
    commands
else
    commands
fi
```

The commands under the `then` statement will be executed
only if the command after `if` statement is a valid command
and the exit status is zero.

### 2.2 Testing Conditions

Besides commands, bash provides a way to test conditions.
Note that there is a space after the first bracket and
a space before the last bracket.

```bash
if [ condition ]; then
    commands
fi
```

There are three types of condition:

* numeric comparison
* string comparison
* file comparison

However, the double parentheses command provides advanced features for numeric
comparison, including most operators in C and `**` for exponentiation.
It'd be better to use the double parentheses command rather than the brackets.

```bash
if (( expression )); then
    commands
fi
```

And for string comparisons,
the double brackets command provides powerful pattern matching feature.
It is the better choice.

```bash
if [[ comparison ]]; then
    commands
fi
```

The file comparisons are the most powerful and most used comparisons in shell
scripting.
They allow you to test the status of files and directories.

| Comparison      | Description                                            |
|-----------------|--------------------------------------------------------|
| -e file         | Check if file exists                                   |
| -d file         | Check if file exists and is a directory                |
| -f file         | Check if file exists and is a file                     |
| -r file         | Check if file exists and is readable                   |
| -w file         | Check if file exists and is writable                   |
| -x file         | Check if file exists and is executable                 |
| -s file         | Check if file exists and is not empty                  |
| -O file         | Check if file exists and is owned by the current user  |
| -G file         | Check if file exists and is owned by the current group |
| file1 -nt file2 | Check if file1 is newer than file2                     |
| file1 -ot file2 | Check if file1 is older than file2                     |

In addition, you can combine conditions using AND (&&) and OR (\|\|).

### 2.3 Using case Statements

The bash provides a elif statement.
However, the case statement is better.

```bash
case variable in
    pattern1)
        commands
        ;;
    pattern2 | pattern3)
        commands
        ;;
    *)
        commands
        ;;
esac
```

## 3. Looping Statements

### 3.1 Using for Statements

```bash
for var in list; do
    commands
done
```

Use the backslash to escape the some special character.
Use double quotation marks to include spaces in one value.
The list can also be the output of a command or wildcards.

```bash
# output of a command
for var in $(command); do
    commands
done

# wildcards
for var in *.py
    commands
done
```

Change the value of `IFS` to change separator temporarily.

```bash
IFS.OLD=$IFS
IFS=$'\n':;"
commands
IFS=$IFS.OLD
```

Bash also provides the C-style `for` statements.

```bash
for (( variable assignment ; condition ; iteration process )); do
    commands
done
```

### 3.2 Using while and until Statements

```bash
# while
while command; do
    commands
done

# until
until command; do
    commands
done
```

### 3.3 Control the Loop

Two commands are used to control what happens inside of a loop:

* `break`
* `continue`

`break n` can indicates the level of the loop to break out of.
By default n is 1.

### 3.4 Processing the Output of a Loop

You can redirect or pipe the output of a loop within the script.

```bash
# redirect
for var in list; do
    commands
done > output.txt

# pipe
for var in list; do
    commands
done | grep txt
```

## 4. Handling User Input

### 4.1 Passing Parameters

Use `$1`, `$2` and so on to refer to the command line parameters.
If the number is larger than 9, use `${n}`.
`$0` stands for the script name.
`$#` contains the number of command line parameters.
`${!#}` contains the last parameter.
Both `$*` and `$@` include all the command line parameters.
The former takes all the parameters as a single word,
while the latter takes all the parameters as separate words.

### 4.2 Being Shifty

`shift` moves each parameter variable one position to the left by default.
For example, after `shift` command,
`$3` refer to the fourth parameter,
`$2` refer to the third parameter,
and `$1` refer to the second parameter.
Note that `$0` always refers to the script name.

You can use `shift` to work with options.

```bash
while [ -n "$1" ]; do
    case "$1" in
        -a)
            commands
            ;;
        *)
            echo "Error"
            ;;
    esac
    shift
done
```

The `shift` command can change the values of `$*` and `$@`.
This is used to separate options from other parameters.

```bash
while [ -n "$1" ]; do
    case "$1" in
        -a)
            commands
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Error"
            ;;
    esac
    shift
done

for var in "$@"; do
    commands
done
```

For some options with values, here is an example:

```bash
while [ -n "$1" ]; do
    case "$1" in
        -a)
            commands
            ;;
        -b)
            val="$2"
            shift
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Error"
            ;;
    esac
    shift
done

for var in "$@"; do
    commands
done
```

There is another parameter style, such as `tar -xvf foobar.tar`.
`getopt` can help.

```bash
getopt optstring paramters
```

The `optstring` list valid option letters.
A colon is placed after option letters requiring a value.
Here is an example.

```bash
set -- $(getopt -q a:b "$@")
while [ -n "$1" ]; do
    case "$1" in
        -a)
            commands
            ;;
        -b)
            val="$2"
            shift
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Error"
            ;;
    esac
    shift
done

for var in "$@"; do
    commands
done
```

`getopts` provides more features.

```bash
getopts optstring variable
```

Instead of producing output for all parameters,
`getopts` works on parameters sequentially.
The `getopts` command uses `OPTARG` to contain the value,
and `OPTIND` to contain the current location where `getopts` left off.
Here is an example.

```bash
while getopts a:b opt; do
    case "$opt" in
        a)
            commands
            ;;
        b)
            echo "The value has been passed to $OPTARG"
            ;;
        *)
            echo "Error"
            ;;
    esac
done

shift $[$OPTIND-1]
for var in "$@"; do
    commands
done
```

### 4.3 Getting User Input

There are two forms of `read` command:

```bash
# form 1
echo -n question
read var

# form 2
read -p question var
```

Use `-t` option to deal with timing out.

```bash
echo -n question
if read -t seconds var; then
    commands
else
    echo "Time out!"
fi
```

Use `-n` option to limit the input size.

```bash
# accept only one character
read -n1 var
```

Use `-s` option to prevent the input from displaying on the monitor.

```bash
echo -n "Enter your password: "
read -s passwd
```

Read from file.

```bash
cat file.txt | while read line; do
    commands
done
```

## 5. Presenting Data

The bash shell reserves three file descriptors:
STDIN (0), STDOUT (1) and STDERR (2).

### 5.1 Redirecting Errors

Only redirect errors.

```bash
command 2> outputfile
```

Redirect both errors and data.

```bash
command 1> outputfile1 2> outputfile2
```

Redirect errors and data to one file

```bash
command &> outputfile
```

### 5.2 Redirecting to a File Descriptor

```bash
command >&2
```

### 5.3 Redirecting File Descriptors

Redirecting a file descriptor to files.

```bash
exec 1>outputfile
exec 0<inputfile
```

### 5.4 Creating Your Own File Descriptors

```bash
exec 3>outputfile
exec 4<inputfile
exec 5<>rwfile
```

Save the STDIN/STDOUT file descriptor location to another file descriptor
temporarily to read/write a file.

```bash
exec 3>&1
exec 1>outputfile
echo "hello world" # write to file
exec 1>&3
echo "hello world" # write to STDOUT

exec 3<&0
exec 0<inputfile
read var # read from file
exec 0<&3
read var # read from STDIN
```

Close file descriptors.

```bash
exec 3>&-
```

### 5.5 Suppressing Output

```bash
command > /dev/null
```

### 5.6 Using Temporary Files

Creating a temporary file.

```bash
mktemp filename.XXXXXX.log
mktemp -t filename.XXXXXX.log
```

### 5.7 Logging Messages

Sending output both to the monitor and to a file.

```bash
command | tee filename
```

## 6. Script Control

### 6.1 Handling Signals

The bash shell allows you to generate two basic signals using key combinations:

* Ctrl+C to interrupt a process
* Ctrl+Z to Pause a process

To trap signals in a script:

```bash
trap command signals

# the trap can be modified
trap command2 signals

# also can be removed
trap -- signals
```

To trap a script exit

```bash
trap commands EXIT
```

### 6.2 Running Scripts in Background Mode

To run a shell script in background mode,
just place an ampersand symbol.

```bash
command &
```

Note that it still uses your terminal monitor for STDOUT and STDERR.
If the terminal session exits, the background process also exits.

### 6.3 Running Scripts without a Hang-Up

To let the script run in background mode until it finishes,
even if exiting the terminal session:

```bash
nohup command &
```

The `nohup` command disassociates the process from the terminal,
thus it redirects STDOUT and STDERR to `nohup.out` file.
Be careful if you run multiple commands using `nohup`.
All output is appended to `nohup.out`.

### 6.4 Controlling the Job

List the current jobs handled by the shell.

```bash
jobs -l
```

Restart the stopped jobs.

```bash
# in background mode
bg job_number

# in foreground mode
fg job_number
```

### 6.5 Being Nice

Set the scheduling priority.

```bash
nice -n priority
renice -n priority -p pid
```

### 6.6 Running Like Clockwork

Schedule a job at some time.

```bash
at -f filename time

# list pending jobs
atq

# rm job from pending job queue
atrm job_number
```

Schedule regular jobs

```bash
min hour dayofmonth month dayofweek command
```

## 7. Creating Functions

### 7.1 Basic Script Functions

There are two form to define a function:

```bash
# form 1
function name {
    commands
}

#form 2
name() {
    commands
}
```

### 7.2 Returning a Value

The return statement is different from other programming languages.
It return the exit status of the function.
The exit status must be in the range of 0 to 255.

```bash
function func {
    commands
    return 0
}

func
echo "The exit status is $?"
```

To return a value like other programming languages,
you can capture the output of the function.

```bash
function func {
    commands
    echo output
}

output=$(func)
```

### 7.3 Using Variables in Functions

The bash shell treats functions just like scripts.
You can pass parameters to a function just like a regular script.

```bash
function func {
    command
}

func parameters
```

Parameters can also be passed to functions via global variables.

```bash
var=1

function func {
    var=2
}

func
# var=2
echo "var=$var"
```

To avoid modifying the value defined outside functions,
you can use loal variables.

```bash
var=1

function func {
    local var=2
}

func
# var=1
echo "var=$var"
```

### 7.4 Array Variables and Functions

Passing an array to a function is very confusing.

```bash
function func {
    local newarray
    newarray=(;'echo "$@"')
    echo ${newarray[*]}
}

myarray1=(1 2 3)
myarray2=($(func $myarray1))
```

### 7.5 Function Recursion

```bash
function func {
    commands
    var=$(func)
    commands
}
```

### 7.6 Creating a Library

To use functions defined in other script file:

```bash
source script_file.sh
```

### 7.7 Using Functions on the Command Line

Define functions in the `.bashrc` file,
then you can use them in the command line.
