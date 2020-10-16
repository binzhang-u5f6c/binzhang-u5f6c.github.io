---
title: "Sed and Awk"
date: 2020-09-30T21:50:47+08:00
draft: true
toc: true
mathjax: false
tags:
  - linux
---

## 1. Basic sed

The format for using `sed`:

```bash
# read from stdin and execute command
sed 'command'

# read from a file and execute command
sed 'command' filename

# read from a file and execute multiple commands
sed -e 'command1; command2' filename
sed -e '
command1
command2' filename

# read from a file and execute commands in a script
sed -f scriptname.sed filename
```

You can specify lines to which the command is applied using address.
The address can be a line number `2`, a range of lines `2,3`.

```bash
sed '2,3command'
# OR
sed -e '2 {
commands
}'
```

You can also filter lines using text pattern.

```bash
sed '/pattern/command'
```

Substituting command.

```bash
sed 's/string1/string2/[flag]' file
```

The command only replace the first occurrence in each line.
Using flag `g` to replace all occurrences.

Delete command is usually in conjunction with address or text pattern.

```bash
sed '2d' filename
sed '3,$d' filename
sed '/number 1/d' filename
```

Inserting and appending commands.

```bash
# insert before line
sed '[address]i\newline' filename

# append after line
sed '[address]a\newline' filename
```

Changing command.

```bash
sed '[address]c\newline' filename
```

Transforming command.

```bash
sed '[address]y/inchars/outchars/' filename
```

Printing command. Use option `-n` to ignore all output except printing command.

```bash
# print the line
sed -n '[address]p' filename

# print the line number
sed -n '[address]=' filename

# print the line with nonprintable characters
sed -n '[address]l' filename
```

Writing command. Note that the sed **do not** change the content in files.

```bash
sed '[address]w filename2' filename1
```

Read and append the content from a file.

```bash
sed '[address]r filename2' filename1
```

## 2. Basic awk

The awk program is similar to sed, but more powerful.
It read the contents line by line and executes script.
Rather than just editor commands, awk provides a programming language.

```bash
# read from stdin and execute command
awk '{script}'

# read from a file and execute {script}
awk '{script}' filename

# read from a file and execute multiple commands
awk 'script1; script2' filename
awk '{
script1
script2}' filename

# read from a file and execute {script}s in a script
awk -f scriptname.awk filename
```

One of the primary features of awk is to manipulate data in the text file.
Awk assigns `$0` to represent the entire line,
and `$n` to represent the nth data field.
The data field is the contents separated by a **field separation character**.
The default field separation character is any whitespace character,
such as tab or space characters.

```bash
# print the first data field of each line
awk '{print $1}'
```

You can use a different field separation character by `-F` option.

```bash
# print the first data field separated by ':' of each line
awk -F: '{print $1}'
```

Similar to sed, you can filter lines using text pattern.

```bash
awk '/pattern/{script}'
```

Some scripts can be run before or after processing data.

```bash
awk 'BEGIN {script1}
{script2}
END {script3}'
```

## 3. Regular expressions

Regular expressions are keys to advanced sed and awk.
A regular expression is a pattern template you define to filter text.

### 3.1 Basic regular expressions

The simplest regular expression is plain text.
Remember that regular expression patterns are **case sensitive**.

```bash
# filter lines with word 'hello'
sed -n '/hello/p'
awk '/hello/{print $0}'
```

These special characters are recognized by regular expressions:

```plain
.*[]^${}\+?|()
```

The backslash(\\) is used to escape special characters.

```bash
sed -n '/\$/p'
```

Although forwardslash is not a special character,
it is used to separate the pattern and command/script in sed/awk.
Thus you need to escape it before use it

```bash
sed -n '/\//p'
```

Anchor characters include the caret (^) and the dollar sign ($).

```bash
# filter lines begin with the word 'hello'
sed -n '/^hello/p'

# filter lines end with the word 'world'
sed -n '/world$/p'
```

**Note**: if the caret is not at the beginning,
or the dollar sign is not at the end,
they do not act as special characters.
which means you do not need to escape them.

The dot (.) is used to match any single character except a newline character.

```bash
# filter lines with '.at', such as 'cat', 'hat', and even ' at'
sed -n '/.at/p'
```

Square brackets are used to define a class of characters.

```bash
# filter lines with only 'cat' or 'hat'
sed -n '/[ch]at/p'

# filter lines with '.at' but except 'cat' nor 'hat'
sed -n '/[^ch]at/p'

# filter lines with 'hello' non-case-sensitively
sed -n '/[Hh][Ee][Ll][Ll][Oo]/p'
```

Use ranges to simplify the character class.

```bash
# filter lines with any digit
sed -n '/[0-9]/p'

# filter lines with any 'a', 'b', 'c', 'x', 'y' or 'z'
sed -n '/[a-cx-z]/p'
```

There are also some special character classes:

* `[[:alpha:]]` means `[a-zA-Z]`
* `[[:alnum:]]` means `[a-zA-Z0-9]`
* `[[:blank:]]` means `[ \t]`
* `[[:digit:]]` means `[0-9]`
* `[[:lower:]]` means `[a-z]`
* `[[:upper:]]` means `[A-Z]`
* `[[:print:]]` means any printable character
* `[[:punct:]]` means any punctuation character
* `[[:space:]]` means any whitespace character: Space, Tab, NL, FF, VT, CR

Placing an asterisk after a character signifies that the character must appear
zero or more times.

```bash
# filter lines with 'scho*l', such as 'schl', 'schol' and 'school'.
sed -n '/scho*l/p'

# combined with dot
sed -n '/sch.*l/p'

# combined with character class
sed -n '/sch[oO]*l/p'
```

### 3.2 Extended regular expressions

Extended regular expressions provide more features.
But they are not supported by sed.

The question mark is used after a character to signify that the character must
appear zero or one time.

```bash
# filter lines with 'bt' or 'bet'
sed -n '/be?t/p'
```

The plus sign is used after a character to signify that the character must
appear one or more times.

```bash
# filter lines with 'bet', 'beet' or 'beeeeet'
sed -n '/be+t/p'
```

Curly braces are used to specify a limit on a repeatable pattern.

```bash
# filter lines with 'beet'
sed -n '/be{2}t/p'

# filter lines with 'beet' or 'beeet'
sed -n '/be{2,3}t/p'
```

The pipe symbol (\|) is used to combine two or more patterns via logical OR.

```bash
# filter lines with 'cat' or 'dog'
sed -n '/cat|dog/p'
```

Parentheses are used to group a pattern in another pattern.

```bash
# here the pattern 'urday' acts as a character
sed -n '/Sat(urday)?/p'
```
