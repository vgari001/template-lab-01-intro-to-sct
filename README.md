# Intro to Linux

> Author(s): Andrew Lvovsky (@borninla) and Brian Crites (@brrcrites)

Welcome to CS 100! For the first lab of the course, we are introducing the basics needed to get around the Linux environment. There are many reasons Linux is still used in industry today, ranging from its open-source nature to its excellent stability. There are various versions of Unix/Linux, known as distributions, which are built from a relatively small number of base kernels. In this course, we will be logging into and using a server provided by the university named `hammer`. In addition to learning Linux basics we will also (re-)introducing g++, CMake, Git and GitHub in this lab.

> Note: any text surrounded by angle brackets `< >` represent a portion of text that is specific to you and needs to be replaced. Make sure you replace that portion with what is requested in the lab description.

## SSH into Hammer 

You will need to log into the `hammer` server using a [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol) application.

If you are using a Linux or Mac computer, you can run the following command in the terminal:

```
ssh <your_CS_username>@hammer.cs.ucr.edu
```

If you are using a Windows computer, you will first need to install a program called PuTTY, which can be installed from [putty.org](http://www.putty.org/). When you open PuTTY, there will be a box for a “Host Name”, where you will input `<your_CS_username>@hammer.cs.ucr.edu`.

> For both of the above commands you should replace <your_CS_username> with the username provided to you by the CS department. If you do not yet have a CS account ask your TA to help you register a new account (some students will need to go through CS systems to get an account, your TA will advise you if this is the case).

You may be asked to exchange keys with the server (which you should allow). Next, you will be prompted for your password. You should note that when typing your password, no characters will be displayed even though you are still typing (this is a security measure).

## The Linux File System

The Linux file system is similar to most other file systems. It is helpful to envision a file system like a tree. You have a root directory (node), denoted simply by `/` in Linux and Mac and typically `C:\` in windows, which has many children that are directories that all live in the root directory. Each of those directories can then hold other directores, which can hold other directories, etc. with as many levels as the user would like.

When you logged into `hammer`, you should've been placed into your user root directory (also called your home directory). To verify this, please type `pwd`, the command to "print working directory" or the current directory you are in. You should see the path `/home/csmajs/<your_CS_username>` (where `<your_CS_username>` is your CS username from before). If your path is not similar, type `cd ~` where the `~` character is a special character reserved by your terminal to represent your personal home directory. `cd` is the command used to "change directories" and expects a relative or absolute path as an argument (we will explain the difference between paths shortly).

One important thing to note at this point is that you are working on a server which can, and likely currently does, have multiple users connected to it at the same time. You can type the command `who` to see "who" is currently logged into the server. Each of these users has their own username, directories, processes, and resources on the server. This means that each user has a different `~` home directory that typing `cd ~` will take them to. This is an important concept to remember in order to develop programs which are portable and will work across users, but more on that later.

Go ahead and type the following command:

```
mkdir example_dir
```

This will create a new directory in your current directory, which (if you ran the last command) should be your home directory. To change into the new directory type `cd example_dir`. Then type `pwd` and you should see your path updated to something like `/home/csmajs/<your_CS_username>/example_dir`. To go back to the directory above, which should be your home direcotry, type `cd ..`. The two dots `..` represent a reserved directory that denotes the directory above your current directory, known as the parent. There is another reserved single dot directory `.` which represents the current working directory and we'll cover its usage later in this lab. Reserved directories like these make it easy to move up and down the directory structure in a relative manner without having to type the full path to the directory each time.

Now, type the following command:

```
cd /home/csmajs/<your_CS_username>/example_dir
```

Notice that you are in the same directory as before (you can verify by running `pwd`). The only difference was which type of path you passed in. In the prior case the `example_dir` in the command `cd example_dir` was a relative path, since you are changing directories *relative to* your current directory. The path `/home/csmajs/<your_CS_username>/exampale_dir` used in `cd /home/csmajs/<your_CS_username>/example_dir` is an absolute path, since it starts with the root directory `/`. All paths which start from the root directory `/` are absolute paths, otherwise the path is relative, although in both cases the path could be valid or invalid.

> Note: Your user root (home) directory is different from the server's root directory. Your home directory is attached to your account and resides as a directory specifically created to hold your files (directories like these are automatically created when new users are added). The system's root directory is the highest (or lowest depending on your view) path on the server and has no parent directory (if you `cd ..` from `/` it will send you back to `/`).

Go back to your home directory (`cd ~`) and type the command `ls` which lists the contents of a directory. You should see the directory `example_dir` we created before as the only entry. Now type `ls -a` and you should see additional "hidden" files which start with a dot (`.`). Any file (even ones you create) that start with a dot are considered hidden files by the system and won't be shown with a normal `ls` command. These files are typically system or configuration files which are used by various systems, such as version control or build systems, or Linux itself, such as `.` and `..`. 

Now that we've demonstrated how to create and traverse directories, let's go ahead and delete `example_dir`. Make sure you are in your home directory, then type:

```
rm -rf example_dir
```

`rm` is the command used to "remove" files or directories. Normally, if you have a file, you can just enter `rm <filename>`. Since we are dealing with a directory, we need to add the extra `-rf` option flags. `r` recursively removes files and directories relative to the directory you are removing, while 'f' ignores nonexistent files and doesn't prompt the user to delete every file within the directory that needs to be deleted. That said, with great power comes great responsibility. Use `rm -rf` only when needed. The last thing you want is to accidently delete important files or directories.

## The `bashrc` File

Another example of a hidden file used by a system is the `.bashrc` file, which you should see when running `ls -a` from your home directory (`~`). The `bashrc` file is a config file that is executed every time your terminal loads such as when you login to `hammer` over SSH. Go ahead and type `cat ~/.bashrc` to view its contents. You should see something like below:

```
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
```

The `cat` command is short for "concatenate" which has a lot of different uses, but here we are using it to output the contents of the file to the terminal. 

This server uses a version of CentOS Linux, which is a Linux distribution that has the benefit of being extremely stable. The problem with this version of Linux is that in order to keep it stable, the CentOS developers don't update the available software often. In this class (and very likely in industry) you will use Git to version, coordinate, and submit your code to online storage, known as a repository. The README you are currently viewing is actually stored in a git repo. The version of the Git tool that is currently available as the default on `hammer` is not compatible with the online service GitHub, so you will need to enable an updated version of the tool for your account with the following command in your terminal:

```
$ source /opt/rh/devtoolset-6/enable
```

This will allow you to use a newer version of Git that is compatible with GitHub as well as give you access to a compiler which has the C++11 standard which we will need for this class. You would normally need to run this command every time you login to `hammer`, which is not ideal. Instead, you will modify the `.bashrc` file to run this command for you every time your open a terminal, which will require you to learn the basics of a command line text editor such as Vim or Emacs.

> Note: If you are unfamiliar with command line editors, you should read these tutorials for [Vim](https://www.linux.com/tutorials/vim-101-beginners-guide-vim/) or [Emacs](http://www.jesshamrick.com/2012/09/10/absolute-beginners-guide-to-emacs/) to get started. Knowing at least one command line editor is extremely important since most servers (especially cloud-based server instances) do not have visual interfaces, so the only way to make changes to files on them is through a command line editor.

We will be using Vim here to add the necessary code to the `.bashrc` file. Make sure you are in your home directory and type `vim .bashrc` to open the `.bashrc` file for editing in Vim. Vim is designed to allow programmers to work quickly, so it tries to keep you hands on the home row of the keyboard as much as possible. It uses the `j` key to move down, the `k` key to move up, the `l` key to move right, and the `h` key to move left. Move down to the line that has `# User specific aliases and functions` using these keys. Next, hit `Shift-A`. This will move your cursor to the end of the line and put you in Insert mode. Vim has multiple modes, the major ones of which are normal mode which takes key commands as as `hjkl` to move or `Shift-A`, and insertion mode, which allows you to type text like you would in a word document (there is also a visual mode which is very useful, but we won't cover it here). You can tell if you are in insert mode by the **-- INSERT --** at the bottom of your window and can use the Esc key to go back to normal mode. 

Go ahead and press Enter while in Insert mode to go to (and create) the next line. Type in `source /opt/rh/devtoolset-6/enable`, then hit the Esc key to go back to normal mode. Type `:` to enter a command sequence followed by `wq` which represent writing the file (`w`) and quiting the program (`q`) and hit Enter. The `:` puts you in command mode which is very powerful in Vim and will let you do everything from [replace all behavior](https://www.linux.com/tutorials/vim-tips-basics-search-and-replace/) to [running bash commands without exiting Vim](https://www.linux.com/tutorials/vim-tips-working-external-commands/). Congrats! You have added your first user-specific command into `.bashrc`. Now, run `source .bashrc` to make sure the new devtools are loaded (you need to do this to reload the file, otherwise you would need to quit `hammer` and log back in for the `.bashrc` file to load).

Another useful tool is aliasing. Aliasing works well when you have a complicated and/or elongated command to type in that you can shorten to something more recognizable. Think of passing a really long URL through [bit.ly](https://bitly.com). Let's look at a way to use aliasing.

`ls -al` is a nifty command to have, but wouldn't it be cool to type something shorter like `la` instead? Let's go ahead and try that. Repeat the steps above with opening up `.bashrc` in Vim. Type the following command on the next line (after where you put the source commmand):

```
alias la="ls -al"
```

Save and exit the file, then run `source .bashrc`. Now you can use `la`! Notice that this alias doesn't replace `ls -al`; you can still use the original form if you so choose.

For students with Linux and macOS machines: try making an alias in your local machine's `.bashrc` file for logging into `hammer`. Something like this will work:

```
alias hammer="ssh <your_CS_username>@hammer.cs.ucr.edu"
```

## Using `g++` for Compilation

Using `g++` invokes GNU's C++ compiler. To demonstrate how it works, go ahead and write a simple `hello world!` program using the command line editor of your choice (save the file as `main.cpp`):

```
#include <iostream>

using namespace std;

int main()
{
	cout << "hello world!" << endl;
	return 0;
}
```

Once written, make sure to save and quit out of your editor. Then, type the following command:

```
g++ main.cpp
```

If successful, there shouldn't be any output. A quick `ls` will show that a new file named `a.out` was generated. This is the executable target file of your program. To run it, type `./a.out` and press Enter. You should now see `hello world!` as output.

Most of the time, you would want to give your program a recognizable name. To do that, we can use the `-o` flag to name our executable target file. Go ahead and type `rm a.out`.

### Extra Stuff

Note from Andrew: Not sure where to put the following note just yet. Maybe near the end of the lab?

> Note: You are not required to develop on the `hammer` server for this course, and are encouraged to use your own development environment. However, you will need to validate that your code will run correctly on the `hammer` server as we cannot account for differences in everyone's individual development environments. You are required to host your code on GitHub.
