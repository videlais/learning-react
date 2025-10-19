---
title: "Introduction to the Command Line"
order: 1
chapter_number: 1
layout: chapter
---

## Objectives

In this chapter, readers will:

- **Identify** the appropriate command-line interface for their operating system (Windows, macOS, or Linux).
- **Define** key command-line terminology including directories, paths, and file system concepts.
- **Apply** essential navigation and file manipulation commands to manage projects effectively.
- **Analyze** the differences between absolute and relative paths in file system navigation.
- **Execute** basic file operations including creating, viewing, copying, moving, and deleting files.
- **Utilize** command-line productivity features like autocomplete and command history.
- **Troubleshoot** common command-line errors and issues.

## Chapter Outline

- [Objectives](#objectives)
- [Chapter Outline](#chapter-outline)
- [Command-Line](#command-line)
- [Why Developers Use the Command Line](#why-developers-use-the-command-line)
- [Finding the Command Line](#finding-the-command-line)
  - [Windows 11 and Windows 10](#windows-11-and-windows-10)
  - [macOS](#macos)
  - [Linux](#linux)
- [Terms](#terms)
- [Understanding Command Syntax](#understanding-command-syntax)
- [Navigation Commands](#navigation-commands)
  - [`ls`](#ls)
  - [`cd`](#cd)
  - [`pwd`](#pwd)
- [Directory Management](#directory-management)
  - [`mkdir`](#mkdir)
  - [`rmdir`](#rmdir)
- [File Operations](#file-operations)
  - [Creating Files](#creating-files)
  - [Viewing File Contents](#viewing-file-contents)
  - [Copying Files](#copying-files)
  - [Moving and Renaming Files](#moving-and-renaming-files)
  - [Deleting Files](#deleting-files)
- [Productivity Tips](#productivity-tips)
  - [Tab Completion](#tab-completion)
  - [Command History](#command-history)
  - [Clearing the Screen](#clearing-the-screen)
- [Introduction to Package Managers](#introduction-to-package-managers)
  - [npm (Node Package Manager)](#npm-node-package-manager)
  - [npx (Node Package Execute)](#npx-node-package-execute)
  - [Verifying Installation](#verifying-installation)
- [Common Issues and Troubleshooting](#common-issues-and-troubleshooting)
  - ["Command Not Found" Error](#command-not-found-error)
  - [Permission Denied Errors](#permission-denied-errors)
  - [Wrong Directory](#wrong-directory)
  - [File or Directory Not Found](#file-or-directory-not-found)
  - [Accidental Command Execution](#accidental-command-execution)
  - [Can't Exit a Program](#cant-exit-a-program)
- [Platform-Specific Notes](#platform-specific-notes)
  - [Windows Considerations](#windows-considerations)
  - [macOS Considerations](#macos-considerations)
  - [Linux Considerations](#linux-considerations)
- [Summary](#summary)

## Command-Line

When thinking of programming, many people imagine a person sitting in the dark and staring at lines of text scrolling on a screen. While this is not the truth for nearly all of modern programming, it does contain a single grain of truth: text on the screen.

Before we can move on to working with React, we have to understand something else: the command-line. Instead of clicking on buttons or dragging-and-dropping, we will be typing in code and then using commands to run, test, and build projects.

## Why Developers Use the Command Line

Modern React development relies heavily on the command line for several critical tasks:

- **Package Management**: Installing and managing libraries and dependencies using npm (Node Package Manager)
- **Development Servers**: Starting local development servers to preview applications
- **Build Tools**: Compiling and optimizing code for production deployment
- **Version Control**: Using Git to track changes and collaborate with teams
- **Automation**: Running tests, linting code, and executing build scripts
- **Efficiency**: Performing repetitive tasks quickly through commands rather than GUI navigation

While graphical interfaces exist for many of these tasks, the command line provides more control, speed, and is often required by modern development workflows. Throughout this book, you'll use commands like `npm install`, `npm start`, and `npm run build` regularly when working with React applications.

## Finding the Command Line

Each operating system defines access to the command line in different ways. In Windows 11, the default is Windows Terminal, while Windows 10 uses PowerShell by default. In macOS, this is Terminal. In different versions of Linux, this might be called the shell or terminal.

### Windows 11 and Windows 10

**Windows 11:** In the search bar, type "terminal" and click on "Terminal" from the search results. This opens Windows Terminal, which is the modern command-line application.

**Windows 10:** In the search bar, type "powershell" and click on "Windows PowerShell" from the search results.

**Alternative for Developers:** Many developers also use Windows Subsystem for Linux (WSL), which provides a Linux environment within Windows. To access this, type "wsl" in the search bar after installing it.

(In older versions of Windows, searching for "Command Prompt" will show "Command Prompt". Click this to open.)

### macOS

From the Applications menu, click on Utilities and then Terminal or press Command + Space and type "Terminal". Click to open.

### Linux

Depending on the version and distribution of Linux, this can be found in different places. In Ubuntu with GNOME (one of the most common distributions), click on Activities (or press the Super key) and then type "terminal". Click on the Terminal to open it. In other desktop environments, look for Terminal in the applications menu.

## Terms

Working with the command-line means learning some new terms that are commonly used when describing what is happening.

When working in different operating systems, the terms *file*, *folder*, and *filesystem* are often used.

**Note:** A file is a single document, and a folder is a named collection of them. A filesystem is all of the files and folders on a computer.

When working with the command-line, however, different terms are used. These draw from an older history of accessing tools and writing code before graphical interfaces.

- **Directory**: a folder of other files.
  
- **Current Working Directory**: the folder currently being accessed.

- **Root Directory**: the bottom-most directory. In Windows, this will be `C:\` and in MacOS X and Linux systems, it will be `/`.
  
- **Server Root Directory**: Programs that serve HTML and other files 'serve' from a directory. On the filesystem, this might be `/webserver/documents` but as far as the server knows, this is `/`. The *server root* is what the server sees as `/` and the filesystem sees as a different directory.
  
- **Path**: the location of a file or directory; this can be either *absolute* or *relative* path. An absolute path includes the root directory and everything else up to the file or directory's location. A relative path includes symbols that express a path in relationship to another.
  - The period, `.`, defines the current directory.
  - Two periods, `..`, defines one directory 'up' from the current one. For example, if the current directory was `/parent/child` and the path was `..`, it would mean `/parent`.
  - The forward slash, `/`, is used to specify files using periods or between directories.
    - For example, to access the file `/parent/file.txt` from the current directory of `/parent/child` the path would be `../file.txt` signaling to go 'up' a directory and then reference the file `file.txt`.
    - When used with directories, the forward slash can specify multiple directories using the slash between them. For example, the path `parent/child` is used to mark the ending of one directory name and the beginning of another inside it.

## Understanding Command Syntax

Commands in the terminal follow a general pattern that helps you understand how to use them effectively:

```bash
command [options] [arguments]
```

- **Command**: The actual program or tool you want to run (e.g., `ls`, `cd`, `mkdir`)
- **Options/Flags**: Modifiers that change how the command behaves, usually preceded by a dash `-` or double dash `--`
- **Arguments**: The targets or inputs for the command (e.g., file names, directory paths)

**Examples:**

```bash
ls -la /home/user
```

- `ls` is the command
- `-la` are combined options (`-l` for long format, `-a` for all files)
- `/home/user` is the argument (the directory to list)

```bash
mkdir -p projects/react/my-app
```

- `mkdir` is the command
- `-p` is an option (create parent directories if they don't exist)
- `projects/react/my-app` is the argument (the directory path to create)

**Common Option Formats:**

- Single letter options: `-a`, `-l`, `-h`
- Combined options: `-la` (same as `-l -a`)
- Long-form options: `--all`, `--help`, `--version`
- Options with values: `-n 10` or `--lines=10`

**Important Notes:**

- Options typically come before arguments
- Square brackets `[]` in documentation mean optional
- Angle brackets `<>` mean required
- Most commands support a `--help` or `-h` option to show usage information

## Navigation Commands

Navigation is the foundation of working with the command line. These commands help you move around the file system and understand where you are.

### `ls`

The command `ls` "lists" the contents of the current directory except for files and directories that start with a period. These are considered "hidden."

```bash
ls
```

When used with the *command-line argument* of `-a`, `ls` will show "all" of the files and directories in the current working directory.

```bash
ls -a
```

Using `-l` provides a "long" format with more details including permissions, owner, size, and modification date:

```bash
ls -l
```

These options can be combined:

```bash
ls -la
```

("Hidden" files and directories are named so because settings, configurations, and other personalization options of many programs are often saved in directories that start with a period, `.`, to 'hide' them from users.)

**Windows PowerShell alternative:** In PowerShell, you can use `dir` or `Get-ChildItem` as alternatives to `ls`.

### `cd`

The command `cd` "changes directory" from the current working directory to another through specifying a path.

```bash
cd /nested/directory
```

Paths are accepted in both absolute, starting with the root directory outward to the current file or directory, or using periods and forward slashes to specify a relative path.

**Common `cd` shortcuts:**

```bash
cd ~        # Go to home directory
cd ..       # Go up one directory
cd ../..    # Go up two directories
cd -        # Go to previous directory (Unix/macOS/Linux)
```

**Windows note:** In Windows, use `cd \` to go to the root of the current drive, or `cd C:\` to go to the C: drive root.

### `pwd`

The command `pwd` "prints working directory" - it displays the full path of the current directory you're in. This is extremely helpful when you're navigating around and need to confirm your location.

```bash
pwd
```

Output example (Unix/macOS/Linux):

```text
/home/username/projects/my-react-app
```

Output example (Windows):

```text
C:\Users\username\projects\my-react-app
```

**Windows PowerShell note:** In PowerShell, the equivalent command is `Get-Location` or simply `pwd` (which works as an alias).

## Directory Management

Creating and removing directories is essential for organizing your projects.

### `mkdir`

The command `mkdir` "makes a directory" based on a path given to it. If a directory does not exist in that location, it is created. If one does, the command fails.

```bash
mkdir newdirectory
```

Paths are accepted in both absolute, starting with the root directory outward to the current file or directory, or using periods and forward slashes to specify a relative path.

**Creating nested directories:**

On Unix/macOS/Linux, use the `-p` flag to create parent directories as needed:

```bash
mkdir -p projects/react/my-app
```

On Windows PowerShell, you can use:

```powershell
mkdir projects\react\my-app
```

(PowerShell automatically creates parent directories)

### `rmdir`

The command `rmdir` "removes directory" - it deletes an empty directory.

**Unix/macOS/Linux:**

```bash
rmdir directoryname
```

To remove a directory and all its contents recursively, use `rm` with the `-r` flag:

```bash
rm -r directoryname
```

**Warning:** Be extremely careful with `rm -r` as it permanently deletes files without sending them to a trash/recycle bin.

**Windows:**

```powershell
rmdir directoryname
```

To remove a directory and all its contents:

```powershell
rmdir /s directoryname
```

## File Operations

Beyond navigation, you'll frequently need to create, view, copy, move, and delete files.

### Creating Files

**Unix/macOS/Linux:**

The `touch` command creates an empty file or updates the timestamp of an existing file:

```bash
touch filename.txt
```

Create multiple files at once:

```bash
touch file1.txt file2.txt file3.txt
```

**Windows PowerShell:**

```powershell
New-Item filename.txt -ItemType File
```

Or use the redirection operator:

```powershell
echo $null > filename.txt
```

**Windows Command Prompt:**

```cmd
type nul > filename.txt
```

### Viewing File Contents

**Unix/macOS/Linux:**

The `cat` command displays the entire contents of a file:

```bash
cat filename.txt
```

For longer files, use `less` which allows scrolling:

```bash
less filename.txt
```

(Press `q` to quit `less`, use arrow keys or space to navigate)

To view just the beginning of a file:

```bash
head filename.txt
```

To view just the end of a file:

```bash
tail filename.txt
```

**Windows PowerShell:**

```powershell
Get-Content filename.txt
```

Or use the alias:

```powershell
cat filename.txt
```

**Windows Command Prompt:**

```cmd
type filename.txt
```

### Copying Files

**Unix/macOS/Linux:**

The `cp` command copies files:

```bash
cp source.txt destination.txt
```

Copy a file to a different directory:

```bash
cp source.txt /path/to/directory/
```

Copy a directory and all its contents recursively:

```bash
cp -r sourcedir destinationdir
```

**Windows PowerShell:**

```powershell
Copy-Item source.txt destination.txt
```

Copy a directory:

```powershell
Copy-Item -Recurse sourcedir destinationdir
```

**Windows Command Prompt:**

```cmd
copy source.txt destination.txt
```

For directories:

```cmd
xcopy sourcedir destinationdir /E /I
```

### Moving and Renaming Files

Moving and renaming are actually the same operation - you're changing the file's path.

**Unix/macOS/Linux:**

The `mv` command moves or renames files:

```bash
mv oldname.txt newname.txt
```

Move to a different directory:

```bash
mv file.txt /path/to/directory/
```

**Windows PowerShell:**

```powershell
Move-Item oldname.txt newname.txt
```

Or use the alias:

```powershell
mv oldname.txt newname.txt
```

**Windows Command Prompt:**

```cmd
move oldname.txt newname.txt
```

Or for renaming:

```cmd
ren oldname.txt newname.txt
```

### Deleting Files

**Warning:** Deleted files typically cannot be recovered, as they don't go to trash/recycle bin. Always double-check before deleting.

**Unix/macOS/Linux:**

The `rm` command removes files:

```bash
rm filename.txt
```

Remove multiple files:

```bash
rm file1.txt file2.txt file3.txt
```

Remove all `.txt` files in current directory:

```bash
rm *.txt
```

**Windows PowerShell:**

```powershell
Remove-Item filename.txt
```

Or use the alias:

```powershell
rm filename.txt
```

**Windows Command Prompt:**

```cmd
del filename.txt
```

## Productivity Tips

These tips will make you much more efficient at the command line.

### Tab Completion

One of the most powerful features is tab completion. When typing a file name, directory name, or command, press the **Tab** key and the shell will attempt to complete it for you.

**Example:**

If you have a directory called `my-react-application`, you can type:

```bash
cd my-re<Tab>
```

And it will complete to:

```bash
cd my-react-application
```

If multiple options exist, pressing Tab twice will show all possibilities.

**Benefits:**

- Saves time typing long names
- Prevents typos
- Helps you discover available files/directories

### Command History

The command line remembers commands you've previously executed.

**Navigation:**

- Press **Up Arrow** to cycle through previous commands
- Press **Down Arrow** to move forward through command history
- Press **Ctrl+R** (Unix/macOS/Linux) to search command history interactively

**Viewing history:**

Unix/macOS/Linux:

```bash
history
```

PowerShell:

```powershell
Get-History
```

Or simply:

```powershell
history
```

**Re-executing commands:**

Unix/macOS/Linux:

```bash
!!          # Execute the last command
!n          # Execute command number n from history
!string     # Execute the most recent command starting with 'string'
```

### Clearing the Screen

When your terminal gets cluttered with output, you can clear it.

**Unix/macOS/Linux:**

```bash
clear
```

Or press **Ctrl+L**

**Windows PowerShell:**

```powershell
Clear-Host
```

Or press **Ctrl+L**, or simply:

```powershell
clear
```

**Windows Command Prompt:**

```cmd
cls
```

**Note:** Clearing the screen doesn't delete command history; it just clears the visible output.

## Introduction to Package Managers

Package managers are essential tools for modern development that automate the process of installing, updating, and managing software libraries and dependencies.

### npm (Node Package Manager)

When working with React, you'll frequently use **npm**, which comes bundled with Node.js. It's the default package manager for JavaScript and is used to:

- Install React and other libraries
- Manage project dependencies
- Run scripts for development and building

**Common npm commands you'll use:**

```bash
npm install          # Install all dependencies listed in package.json
npm install <package> # Install a specific package
npm start           # Start the development server
npm run build       # Build the project for production
npm test            # Run tests
```

### npx (Node Package Execute)

**npx** is a tool that comes with npm (since npm version 5.2.0) and allows you to execute packages without installing them globally.

**Common use case for React:**

```bash
npx create-react-app my-app
```

This creates a new React application without needing to install `create-react-app` globally.

**Benefits of npx:**

- Always uses the latest version of a package
- Doesn't clutter your system with global installations
- Great for one-time-use tools

### Verifying Installation

To check if Node.js and npm are installed:

```bash
node --version
npm --version
npx --version
```

If these commands work, you're ready for React development. If not, you'll need to install Node.js from [nodejs.org](https://nodejs.org/).

**Note:** Later chapters will cover npm and npx in much more detail. For now, just understand that they're command-line tools you'll use frequently for React development.

## Common Issues and Troubleshooting

Here are solutions to problems beginners frequently encounter:

### "Command Not Found" Error

**Problem:** When you type a command, you get an error like:

```text
command not found: npm
```text
or
```text
'npm' is not recognized as an internal or external command
```

**Solutions:**

1. **Verify the command is installed:** Not all commands come pre-installed. For example, `npm` requires Node.js to be installed first.

2. **Check spelling and capitalization:**
   - Unix/macOS/Linux commands are case-sensitive: `ls` works, but `LS` does not
   - Windows commands are typically case-insensitive

3. **Restart your terminal:** After installing new software, you may need to close and reopen your terminal for changes to take effect.

4. **Check your PATH:** The system needs to know where to find the command. This is an advanced topic, but if a program is installed and still not found, it may not be in your system's PATH.

### Permission Denied Errors

**Problem:** You get an error like:

```text
Permission denied
```

**Solutions:**

**Unix/macOS/Linux:**

Use `sudo` to run commands with administrator privileges:

```bash
sudo npm install -g package-name
```

**Warning:** Only use `sudo` when necessary and when you trust the command, as it grants full system access.

**Windows:**

Run PowerShell or Command Prompt as Administrator:

1. Right-click on PowerShell/Command Prompt
2. Select "Run as Administrator"

**Better approach for npm:** Configure npm to use a directory you own rather than requiring administrator access. This is covered in Node.js documentation.

### Wrong Directory

**Problem:** Commands fail because you're not in the correct directory.

**Solution:**

1. Use `pwd` (or `Get-Location` in PowerShell) to see where you are
2. Use `cd` to navigate to the correct directory
3. Many project-specific commands must be run from the project root directory (where `package.json` is located)

**Example:**

```bash
pwd                    # Check current location
cd ~/projects/my-app   # Navigate to project
npm start             # Now this will work
```

### File or Directory Not Found

**Problem:** You try to access a file/directory that doesn't exist or is misspelled.

**Solutions:**

1. Use `ls` (or `dir`) to see what's actually in the current directory
2. Check spelling carefully (remember case sensitivity on Unix/macOS/Linux)
3. Verify you're in the correct directory
4. Use tab completion to avoid typos

### Accidental Command Execution

**Problem:** You run a command that takes too long or you want to cancel.

**Solution:**

Press **Ctrl+C** to cancel/interrupt a running command. This works on all platforms.

### Can't Exit a Program

**Problem:** You're stuck in a program like `less`, `vi`, or `nano`.

**Solutions:**

- **less:** Press `q`
- **vi/vim:** Press `Esc`, then type `:q!` and press Enter
- **nano:** Press `Ctrl+X`
- **Any program:** Try `Ctrl+C` or `Ctrl+D`

## Platform-Specific Notes

### Windows Considerations

**PowerShell vs Command Prompt:**

- **PowerShell** is the modern shell for Windows and is recommended
- **Command Prompt** is the legacy shell (still works but limited)
- Most commands in this book assume PowerShell or Unix-like shells

**Path Separators:**

- Windows uses backslashes: `C:\Users\name\projects`
- Unix/macOS/Linux use forward slashes: `/home/name/projects`
- **Good news:** PowerShell accepts both forward and backward slashes in most cases

**Case Sensitivity:**

- Windows file systems are typically case-insensitive: `File.txt` and `file.txt` are the same
- Unix/macOS/Linux are case-sensitive: `File.txt` and `file.txt` are different files

**Command Aliases:**

PowerShell provides aliases to make it more compatible with Unix commands:

- `ls` → `Get-ChildItem`
- `pwd` → `Get-Location`
- `cat` → `Get-Content`
- `cp` → `Copy-Item`
- `mv` → `Move-Item`
- `rm` → `Remove-Item`

### macOS Considerations

**Homebrew:**

Many developers install [Homebrew](https://brew.sh/), a package manager for macOS that makes installing development tools easier:

```bash
brew install node
```

**Default Shell:**

Modern versions of macOS use **zsh** (Z shell) as the default instead of bash. For most purposes, they work identically.

### Linux Considerations

**Package Managers:**

Different Linux distributions use different package managers:

- **Ubuntu/Debian:** `apt` or `apt-get`
- **Fedora/RHEL:** `dnf` or `yum`
- **Arch:** `pacman`

**Installing Node.js example (Ubuntu):**

```bash
sudo apt update
sudo apt install nodejs npm
```

**Permissions:**

Linux has robust file permissions. You may need to use `sudo` for system-wide installations, but avoid using `sudo` with npm when possible.

## Summary

In this chapter, you've learned:

- ✅ Why developers use the command line for modern React development
- ✅ How to access the terminal on Windows, macOS, and Linux
- ✅ Essential terminology: directories, paths, root directory, working directory
- ✅ Command syntax structure: commands, options/flags, and arguments
- ✅ Navigation commands: `ls`, `cd`, `pwd`
- ✅ Directory management: `mkdir`, `rmdir`
- ✅ File operations: creating, viewing, copying, moving, and deleting files
- ✅ Productivity features: tab completion, command history, clearing the screen
- ✅ Introduction to npm and npx for package management
- ✅ Common issues and how to troubleshoot them
- ✅ Platform-specific differences between Windows, macOS, and Linux

**Key Takeaways:**

1. The command line is essential for modern React development
2. Master basic navigation and file operations before moving forward
3. Use tab completion and command history to work more efficiently
4. Don't panic when you encounter errors - they usually have simple solutions
5. npm and npx will be your primary tools for React development

**Next Steps:**

Now that you're comfortable with the command line, you're ready to set up your development environment and start building React applications. The next chapter will guide you through installing Node.js, setting up a code editor, and creating your first React project.

**Practice Exercises:**

Try these to reinforce what you've learned:

1. Open your terminal and use `pwd` to see where you are
2. Create a new directory called `practice` using `mkdir`
3. Navigate into it using `cd practice`
4. Create three empty files: `file1.txt`, `file2.txt`, `file3.txt`
5. List the contents to verify the files were created
6. Copy `file1.txt` to `file1-copy.txt`
7. Rename `file2.txt` to `renamed.txt`
8. Delete `file3.txt`
9. Use `cd ..` to go back up one directory
10. Remove the `practice` directory

The more you practice these commands, the more natural they'll become!
