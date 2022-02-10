---
title: Bash Scripts Exploration
date: 2019-07-17 13:52:32
tags: bash
categories: Linux
---

**What is bash script?** 

Bash is a Unix shell, which is a command line interface(CLI) for interacting with the operating system(OS).

Bash script which contains command lines can be run in **Bash** and finish extremely excellent work.

# Prepared work

## Create a bash script 

In Linux operating system, suffix name is not necessary so that we can name the bash script whatever we want. For example, we can create a folder named `scripts` and 
a bash script named `experiment` in it. 
```bash
mkdir scripts
cd scripts
touch experiment
```
In order to access scripts in this folder anywhere, adding this directory to **ENV PATH** is recommended.
```bash
# optional
export PATH=/path/to/scripts:$PATH
```

## Make the bash script executable

To run the `experiment` script, it's necessary to change its mode.
```bash
chmod u+x experiment
```

## Add symbol

Each bash script should begins with the following line for specifying the interpreter.
```bash
#!/bin/bash
```

# Basic syntax

## Echo

Keyword `echo` can be used to output contents to the screen. Add the following line in `experiment` script and run the script, **Hello world** can be seen in the terminal.
```bash
echo Hello world
```

## Variables

Variables must be declared before being invoked. Use `=` to assign variables and use `$` to call them.
```bash
#!/bin/bash
name="Jack"

echo Hello, I am $name
```

## Read

`read` can be used to assign variables from input on ternimal. 
```bash
#!/bin/bash
echo what are you?

read who

echo Hello, $who
```

## Conditions

Structures like `for`, `if...else` can also be used in bash scripts. But operators used in bash are different from those in programming languages.

### Judgment

| Bash Operators | Operators | Description |
| :------------: | :-------: | :---------: |
| -eq | == | equal |
| -ne | != | not equal |
| -gt | >  | greater than |
| -ge | >= | greater than or equal |
| -lt | <  | less than |
| -le | <= | less than or equal |
| -z  | == null | is null |

For example:

```bash
#!/bin/bash
echo How much is it?

read prize

if [ $prize -gt 100 ]
then 
    echo Too expensive
else
    echo Great
fi
```

### Looping

Looping is another powerful structure in programming language, we also can use it in bash scripts as follow.
```bash
#!/bin/bash
FILES=/opt/*

for file in $FILES
do
    echo $(basename $file)
done
```

# Open another terminal

Sometimes multiple screens are required to display different messages. To satisfy this requirment, `gnome-ternimal` can be used to open another ternimal and run commands in it.
Add `gnome-terminal` in a bash script, a new ternimal will open when we run the script.

```bash
#!/bin/bash
gnome-ternimal
```

## Optional arguments

Some arguments are used for customizing the new ternimal.

- `--maximize`: Maximize the ternimal
- `--full-screen`: The terminal Occupy the full screen after opening.
- `--window --window`: Open two terminals
- `--window --tab --window --tab --tab`: Open two ternimals and open tabs in them
- `--geometry=80x25+10+10`: Set size of the terminal 
- `-e`: run the following commands in the new terminal, the following commands should be included in ''. 

Fro example:
```bash
#!/bin/bash
gnome-terminal --geometry=80x25 -e 'echo $PATH'
```

However, the terminal will disappear after executing commands. In order to retain the terminal after executing commands, we can use the following commands to maintain the window.
```bash
#!/bin/bash
gnome-terminal --geometry=80x25 -e 'bash -c "echo $PATH;exec bash"'
```

# References

- [How to Create and Use Bash Scripts](https://www.taniarascia.com/how-to-create-and-use-bash-scripts/)
- [linux下使用shell脚本打开多个terminal并在其中运行程序的方法](https://blog.csdn.net/weixin_39465823/article/details/83786685)
