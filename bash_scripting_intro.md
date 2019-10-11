title: Bash Scripting
author:
  name: Matt Mulhern
  email: mattmulhern01@gmail.com
output: bash_scripting_intro.html
theme: sjaakvandenberg/cleaver-dark

--

# Bash Scripting Basics
## A quick look at bash scripting

--

### Contents
* Hello world
* Variables
* Arithmetic
* Conditionals
* Loops
* Exit codes

--

### Hello World
*helloWorld.sh*:
```bash
#!/bin/bash             # known as a sha-bang, this tells your shell that this is a bash script
echo "Hello World!"     # print soem text to screen
```
Try to run the script:
```bash
$ ./helloWorld.sh
bash: ./helloWorld.sh: Permission denied
```
Why didnt it run?  - *file permissions dont allow execution!*
```bash
$ ./helloWorld.sh
bash: ./helloWorld.sh: Permission denied
$ ls -l helloWorld.sh
-rw-r--r--  1 mmulhern  staff  32 Jul 15 09:20 helloWorld.sh
$ chmod +x helloWorld.sh
$ ls -l helloWorld.sh
-rwxr-xr-x  1 mmulhern  staff  32 Jul 15 09:20 helloWorld.sh
$ ./helloWorld.sh
Hello World!
```

--

### Variables

*variables.sh:*
```bash
#!/bin/bash
NAME="Dave"                              # assign 'Dave' to variable 'NAME'
echo "I can't let you do that ${NAME}"   # curly braces, {} are used to isolate variable name from other text
```
Output (don't forget to `chmod +x` it!):
```bash
$ ./variables.sh
I cant let you do that Dave
```

--

### Arithmetic
* By default, bash treats every variable as a string.
    * To perform meaningful operations on variables, it may be necessary to perform **arithmetic expansion**.
    * done with `$((  ))`

*arithmetic.sh*
```bash
#!/bin/bash
x=100
echo x = $x
echo x + 1 without expansion: $x + 1    # wihtout expansion, x is just a string
echo x + 1 = $((x + 1))                 # double braces, (()), used to perform 'arithmetic expansion' on x (treat it as a number)
echo incrementing x to $((x += 1))      # increment value of x (and return it)
echo x is now set to $x
((x *= 2))                              # note how you don't need the $ if youre not using the value at the time.
echo x is now set to $x
```

```bash
./$ ./arithmetic.sh
x = 100
x + 1 without expansion: 100 + 1
x + 1 = 101
incrementing x to 101
x is now set to 101
x is now set to 202
```

--

### Conditionals
Used for flow control in scripts:
```bash
if [ SOMETHING ]
then
    DO SOMETHING
elif [SOMETHING ELSE]
    DO SOMETHING ELSE
else
    DO ANOTHER DIFFERNET THING
fi
```
--

### Conditionals
*common conditions:*

|||
|:----------|:--------------------------------------------------------------------------|
|**File CHECKS**                                                                        |
|-r FILE    |Check if FILE is readable.                                                 |
|-w FILE    |Check if FILE is writable.                                                 |
|-x FILE    |Check if we have execute access to FILE.                                   |
|-f FILE    |Check if FILE is an ordinary FILE (not a directory, a device FILE, etc.)   |
|-s FILE    |Check if FILE has size greater than 0.                                     |
|-d FILE    |Check if FILE is a directory.                                              |
|-e FILE    |Check if FILE exists.  Is true even if FILE is a directory.                |
|**Arithmetic Checks**                                                                  |
|n1 -eq n2|Check to see if n1 equals n2.                                                |
|n1 -ne n2|Check to see if n1 is not equal to n2.                                       |
|n1 -lt n2|Check to see if n1 < n2.i                                                    |
|n1 -le n2|Check to see if n1 <= n2.                                                    |
|n1 -gt n2|Check to see if n1 > n2.                                                     |
|n1 -ge n2|Check to see if n1 >= n2.                                                    |
|**String Checks**                                                                      |
|s1 = s2  |Check if s1 equals s2.                                                       |
|s1 != s2 |Check if s1 is not equal to s2.                                              |
|-z s1    |Check if s1 has size 0.                                                      |
|-n s1    |Check if s2 has nonzero size.                                                |
|s1       |Check if s1 is not the empty string.                                         |


--

### Conditionals
*conditionals.sh*
```bash
#!/bin/bash
STR=wellHelloThereEveryone!
STR_TO_COMPARE_TO=Hello
NUM=10
FILE="/etc/redhat-release"


# File checks
if [ -r $FILE ]                         # condition to meet
then
    echo "$FILE is readible!"           # action if condition met
else
    echo "$FILE is not readible!"       # action if condition not met
fi                                      # you need to 'close' the conditional statement

# Arithmetic checks
if [ $NUM -gt 5 ]
then
    echo "$NUM is greater than five!"
else
    echo "I can't count"
fi

# String checks
if [[ $STR == $STR_TO_COMPARE_TO ]]             # Note the double brackets, [[]] needed for strings
then
    echo $STR
elif [[ $STR == *${STR_TO_COMPARE_TO}* ]]       # Second condition to check for if first is not met
then
    echo $STR does contain $STR_TO_COMPARE_TO
else                                            # Action if no conditions met
    echo "no match"
fi
```

--

### Loops
* 3 main types available:
    * For
    * While
    * Until
* Carry out set of instructions repeatidly until condition is met.

```bash
#!/bin/bash
COUNTER=0
while [  $COUNTER -lt 5 ]; do               # condition
    echo WHILE: The counter is $COUNTER     # action
    ((COUNTER += 1))                        # increment COUNTER
done                                        # close loop


until [  $COUNTER = 0 ]; do                 # condition
    echo UNTIL: The counter is $COUNTER     # action
    ((COUNTER -= 1))                        # decrement COUNTER
done                                        # close loop

for x in {15..10}; do           # range (note that its INCLUSIVE)
    echo FOR: $x                # action
done                            # close loop
```

```bash
WHILE: The counter is 0
WHILE: The counter is 1
WHILE: The counter is 2
WHILE: The counter is 3
WHILE: The counter is 4
UNTIL: The counter is 5
UNTIL: The counter is 4
UNTIL: The counter is 3
UNTIL: The counter is 2
UNTIL: The counter is 1
FOR: 15
FOR: 14
FOR: 13
FOR: 12
FOR: 11
FOR: 10
```
--

### Loops
For loops can be extremely useful as the "counter" doesnt have to be a number, it can be an arbitrary list of items:

*forItems.sh*:
```bash
#!/bin/bash
# backticks (``) can be used to perform other commands and use their output
for x in `ls`; do               # for each line of output from `ls` command
    echo Found: $x              # action
done                            # close loop
```

--

### Exit codes
* All the scripts shown so far are basicallly a list of commands.
    * Every line is carried out regardless of what happened before.
* *So what if we want to check if something was sucessful?*
    * Return codes can be used!

*typical return codes in bash*

|||
|:--|:----------------------------------------------------------|:--------------------------|
|0  |Sucess                                                     |`ls /tmp`                  |
|1  |Catchall used for generall errors                          |`let "var1 = 1/0"`         |
|126|Command could not be executed (permissions problem usually)|`/dev/null`                |
|127|Command not found                                          |`this_isnt_a_command`      |

```bash
#!/bin/bash

# try to do something here...
echo hello > /etc/passwd
RET_CODE=$?                 # IMMEDIADLY grab $? before it's overwritten

if [ $RET_CODE -gt 0 ]      # check value of RET_CODE for success/failure
then
    echo "Fail! RET_CODE:$RET_CODE"
else
    echo "Success!"
fi
```

```bash
$ ./retCodes.sh
./retCodes.sh: line 4: /etc/passwd: Permission denied
Fail! RET_CODE:1
```
