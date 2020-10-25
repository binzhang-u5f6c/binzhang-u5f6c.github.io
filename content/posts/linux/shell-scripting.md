---
title: "Shell Scripting"
date: 2020-08-06T21:33:42+08:00
draft: false
toc: true
mathjax: false
tags:
  - linux
---

## 1. Basic scripting building

### 1.1 Display messages

Use `echo` to display messages.

```bash
echo "Hello world!"
```

### 1.2 Use variables

Define and reference a variable.

```bash
val=10
echo ${val}
```

In most cases, the curly brackets can be ignored.

```bash
val=str
# ignore curly brackets
echo $val
# ignoring curly brackets will lead to error
echo ${val}ing
```

Define and reference an array.

```bash
a[0]=1
echo ${a[0]}
# or
a = (1 2 3)
echo ${a[0]}
```

**Note**: bash treats all values as string.

### 1.3 Exit

Use `echo $?` to check the exit status.
Use `exit N` to return the status `N`.

## 2. Conditional statements

### 2.1 if-then statements

```bash
if command; then
    commands
elif command; then
    commands
else
    commands
fi
```

The commands under the `then` statement will be executed
only if the command after `if` statement is a valid command
and the exit status is zero.

### 2.2 Testing conditions

Besides commands, bash provides a way to test conditions.
There are three types of conditions:

* numeric comparisons
* string comparisons
* file comparisons

Use double parentheses for numeric comparisons,
including most operators in C and `**` for exponentiation.

```bash
if (( expression )); then
    commands
fi
```

Use double square brackets for string comparisons,
including normal string operators and `=~` for regular expression.

```bash
if [[ comparison ]]; then
    commands
fi
```

The file comparisons are the most powerful and most used comparisons in shell
scripting.

```bash
if [ condition ]; then
    commands
fi
```

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

### 2.3 case statements

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

## 3. Looping statements

### 3.1 for statements

```bash
for var in words; do
    commands
done
```

We can create words by brace expansion, wildcards or command substitution.

```bash
# brace expansion
for var in {a..z}; do
    commands
done

# wildcards
for var in *.txt; do
    commands
done

# command substitution
for var in $(command); do
    commands
done
```

We can change the value of `IFS` to change separator temporarily.

```bash
IFS.OLD=$IFS
IFS=$'\n':;"
commands
IFS=$IFS.OLD
```

Bash also provides the C-style `for` statements.

```bash
for (( expression1; expression2; expression3 )); do
    commands
done
```

### 3.2 while and until statements

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

### 3.3 Control the loop

Two commands are used to control what happens inside of a loop:

* `break`
* `continue`

`break n` can indicate the level of the loop to break out of.
By default n is 1.

### 3.4 Process the output of a loop

You can redirect or pipe the output of a loop within the script.

```bash
# redirect
for var in words; do
    commands
done > output.txt

# pipe
for var in words; do
    commands
done | grep txt
```

## 4. Handle input

### 4.1 Command line parameters

* Use `$0` to refer to the script name.
* Use `$1`, `$2` and so on to refer to the command line parameters.
* If the number is larger than 9, use curly brackets `${n}`.
* `$#` contains the number of parameters.
* Use `${!#}` to refer the last parameter.

Both `$*` and `$@` include all the command line parameters.
The former takes all the parameters as a single word,
while the latter takes all the parameters as separate words.

### 4.2 Being shifty

`shift` moves each parameter variable one position to the left by default.
For example, after `shift` command,
`$3` refers to the fourth parameter,
`$2` refers to the third parameter,
and `$1` refers to the second parameter.
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

Usually the Linux-style command looks like `command options parameter`,
and some options even requre another parameters, such as

```bash
ls -a
rm -rf file
curl -o outputfile url
```

In such cases, `getopts` is a better choice rather than `shift`.

```bash
getopts optstring opt
```

The `optstring` lists all valid option characters.
A colon after a character means the option requires a value.
Each time it is invoked, an option is assigned in `opt`,
and the index of next option is assigned in `OPTIND`.
If the option requres a value, it will be stored in `OPTARG`.
Thus `OPTIND` should be initialized as 1 at first.
Here is an example.

```bash
OPTIND=1
while getopts a:bc opt; do
    case "$opt" in
        a)
            # store value in val
            val=$OPTARG
            commands
            ;;
        b)
            commands
            ;;
        c)
            commands
        *)
            echo "Error"
            ;;
    esac
done

shift $(($OPTIND-1))
for var in "$@"; do
    commands
done
```

### 4.3 Get user input

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

## 5. File descriptors

### 5.1 Redirect to a file descriptor

```bash
command >&2
```

### 5.2 Redirect a file descriptor to files

```bash
exec 1>file1
exec 0<file2
```

### 5.3 Create your own file descriptors

```bash
exec 3>file1
exec 4<file2
exec 5<>file3
```

Save the STDIN/STDOUT file descriptor location to another file descriptor
temporarily to read/write a file.

```bash
exec 3>&1
exec 1>file1
echo "hello world" # write to file1
exec 1>&3
echo "hello world" # write to STDOUT

exec 3<&0
exec 0<file2
read var # read from file2
exec 0<&3
read var # read from STDIN
```

Close file descriptors.

```bash
exec 3>&-
```

## 6. Trap signals

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

## 7. Create functions

### 7.1 Basic script functions

There are two forms to define a function.

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

### 7.2 Return a value

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

### 7.3 Use variables in functions

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
you can use local variables.

```bash
var=1

function func {
    local var=2
}

func
# var=1
echo "var=$var"
```

### 7.4 Array variables and functions

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

### 7.5 Function recursion

```bash
function func {
    commands
    var=$(func)
    commands
}
```

### 7.6 Create a library

To use functions defined in other script files:

```bash
source script_file.sh
```

### 7.7 Use functions on the command line

Define functions in the `.bashrc` file,
then you can use them in the command line.
