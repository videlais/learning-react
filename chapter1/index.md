# Introduction to the Command Line

- [Introduction to the Command Line](#introduction-to-the-command-line)
  - [Command-Line](#command-line)
  - [Finding the Command Line](#finding-the-command-line)
    - [Windows 10](#windows-10)
    - [MacOS X](#macos-x)
    - [Linux](#linux)
  - [Terms](#terms)
  - [Command-Line Tools](#command-line-tools)
    - [`ls`](#ls)
    - [`cd`](#cd)
    - [`mkdir`](#mkdir)

## Command-Line

When thinking of programming, many people imagine the the trope of a person sitting in the dark and staring at lines of text scrolling on a screen. While this is not the truth for nearly all of modern programming, it does contain a single grain of truth: text on the screen.

Before we can move on to working with React, we have to understand something else: the command-line. Instead of clicking on buttons or dragging-and-dropping, we will be typing in code and then commands to run, test, and build projects.

## Finding the Command Line

Each operating system defines access to the command line in different ways. In versions of Windows 10 and later, this is PowerShell. In MacOS X and later, this is Terminal. In different versions of Linux, this might be called the shell or terminal.

### Windows 10

In the search bar, type "powershell". Click on "Windows PowerShell" from the search results. Click this to open.

(In older versions of Windows, searching for "Command Prompt" will show "Command Prompt". Click this to open.)

### MacOS X

From the Applications menu, click on Utilities and then Terminal or press Command + Space and type "Terminal". Click to open.

### Linux

Depending on the version and distribution of Linux, this can be found in different places. In Ubuntu, one of the most common distributions, click on the Dash and then type "terminal". Click on the Terminal to open it.

## Terms

Working with the command-line means learning some new terms that are commonly used when describing what is happening.

When working in different operating systems, the terms *file*, *folder*, and *filesystem* are often used.

**Note:** A file is a single document, and a folder is a named collection of them. A filesystem is all of the files and folders on a computer.

When working with the command-line, different terms are used. These draw from an older history of accessing tools and writing code before graphical interfaces.

- **Directory**: a folder of other files.
  
- **Current Working Directory**: the folder currently being accessed.
- **Root Directory**: the bottom-most directory. In Windows, this will be `C:\` and in MacOS X and Linux systems, it will be `/`.
  
- **Server Root Directory**: Programs that serve HTML and other files 'serve' from a directory. On the filesystem, this might be `/webserver/documents` but as far as the server knows, this is `/`. The *server root* is what the server sees as `/` and the filesystem sees as a different directory.
  
- **Path**: the location of a file or directory; this can be either *absolute* or *relative* path. An absolute path includes the root directory and everything else up to the file or directory's location. A relative path includes symbols that express a path in relationship to another.
  - The period, `.`, defines the current directory.
  - Two periods, `..`, defines one directory 'up' from the current one. For example, if the current directory was `/parent/child` and the path was `..`, it would mean `/parent`.
  - The backward slash, `/`, is used to specify files using periods or between directories.
    - For example, to access the file `/parent/file.txt` from the current directory of `/parent/child` the path would be `../file.txt` signaling to go 'up' a directory and then reference the file `file.txt`.
    - When used with directories, the backward slash can specify multiple directory using the slash between them. For example, the path `parent/child` is used to mark the ending of one directory name and the beginning of another inside it.

## Command-Line Tools

Each operating system is slightly different in how it defines the tools for working with the command-line. However, there are some common tools that exist across most operating systems.

### `ls`

The command `ls` "lists" the contents of the current directory except for files and directories that start with a period. These are considered "hidden."

When used with the *command-line argument* of `-a`, `ls` will show "all" of the files and directories in the current working directory.

("Hidden" files and directories are named so because settings, configurations, and other personalization options or many programs are often saved in directories that start with a period, `.`, to 'hide' them from users.)

### `cd`

The command `cd` "changes directory" from the current working directory to another through specifying a path.

Paths are accepted in both absolute, starting with the root directory outward to the current file or directory, or using periods and backslashes to specify a relative path.

### `mkdir`

The command `mkdir` "makes a directory" based on a path given to it. If a directory does not exist in that location, it is created. It one does, the command fails.

Paths are accepted in both absolute, starting with the root directory outward to the current file or directory, or using periods and backslashes to specify a relative path.
