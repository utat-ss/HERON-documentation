# Command Line

The command line is a useful way to interact with your computer. It allows you to navigate your computer and execute command line programs. As you will see in developing the satellite's software, there are particular programs and functionality that are only available through the command line.

It will probably seem scary and confusing at first, but will become natural as you use it more.

To access the command line, open the Terminal application (Mac) or Command Prompt application (Windows). The Mac command line is called a bash terminal, while the Windows command line is called a cmd terminal.

You always have a **current directory** (folder) where operations take place. It will prompt you with the current directory followed by `$` (Mac) or `>` (Windows) to enter a command.

Here are some common commands. Remember that a folder and a directory are the same thing. Any text inside `<>` is text you replace (without the brackets).

| Mac | Windows | Command |
| :- | :- | :- |
| `pwd <path>` | `chdir <path>` | Print working (current) directory |
| `cd <path>` | `cd <path>` | Change directory |
| `cd ..` | `cd ..` | Go up (back) one directory |
| `ls` | `dir` | List files and folders in current directory |
| `mkdir <name>` | `mkdir <name>` | Make new directory |

When you execute a command (program), sometimes you pass it **command line arguments** to give it additional information or instructions.

For example, `ls` displays just the file and folder names in a directory, while `ls -l` tells it to display more information about the files and folders.
