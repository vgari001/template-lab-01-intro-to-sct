# Intro to Linux

Welcome to CS 100! For the first lab of the course, we are introducing the basics needed to get around the Linux environment. There are many reasons Linux is still used in industry today, ranging from its open-source nature to its robustness in portability. There are various distributions that build on top of the Linux kernel (i.e. Ubuntu, Red Hat, CentOS, etc...). In this course, we will be logging into and using a server called Hammer. We are also introducing CMake, Git and GitHub in this lab.

> Note: any text surrounded by angle brackets `< >` represent a portion of text that is specific to you and needs to be replaced. Make sure you replace that portion with what is requested in the lab description.

## SSH into Hammer 

You will need to log into the Hammer server using [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol). 

If you are using a Linux or Mac computer, you can run the following command in the terminal:

```
ssh <your_CS_username>@hammer.cs.ucr.edu
```

If you are using a Windows computer, you will first need to install a program called PuTTY, which can be installed from [putty.org](http://www.putty.org/). When you open PuTTY, there will be a box for a “Host Name”, where you will input `<your_CS_username>@hammer.cs.ucr.edu`.

You may be asked to exchange keys with the server (which you should allow). Next, you will be prompted for your password. You should note that when typing your password, no characters will be displayed even though you are still typing (this is a security measure).

## Basics with the Linux Environments

Let's go over some basic commands that will help you manipulate and traverse the Linux system.

### Creating Files

Traditionally, `echo` is the first command to learn. Go ahead and type the following in the command prompt, then hit Enter.

```
echo "hello world"
```

This prints `hello world` back to standard (console) output (you can exclude the quotations if you want). You can also redirect output into a file. Go ahead and enter the following command:

```
echo "hello world" > example.txt
```

This command creates a new file named `example.txt` and redirects the `hello world` string into the file.

> Note: Keep in mind that a basic text file in Linux doesn't need to have a .txt encoding to read from or write to (think of file encoding as a convention). You may drop the `.txt` from `example.txt` and simply create a file named `example`. However, if you are using a graphical user interface (GUI) that visualizes your OS system (like Windows or macOS), the OS will ask how you would like to open that file.

`cat` is used to print a file's contents. Go ahead and use `cat` on `example.txt`:

```
cat example.txt
```

This should print `hello world` from the file. You can also concatenate and print other files using `cat` like so:

```
cat <first file> <second file> <third file> ... <nth file>
```

`cat` is a powerful command. We suggest to review other ways to use `cat` by checking out [this blog post](https://www.tecmint.com/13-basic-cat-command-examples-in-linux/) or searching for examples through Google.

Other ways to create files would be by using `touch` (`touch <file name>`) or by opening a new file in a text editor like Vim (`vim <file name>`) and saving it (we will talk about Vim later in this lab).

This is a good time to clear your terminal screen. To do so, simply type and enter `clear`.

### The File System

The Linux file system is similar to the macOS file system. It is helpful to envision a file system like a tree. You have a root node (root directory, denoted by `/`) that have many children (multiple directories that all live in the root directory), and so forth and so on. For Windows, `C:\` would be the root directory.

When you logged into Hammer, you should've been placed into your user root directory (also called your home directory). To verify this, please type `pwd` (command for printing the current directory you are in). You should see the path `/home/csmajs/<your_CS_username>`. If your path is not similar, type `cd ~` (the `~` character is a special character that denotes your home directory).

`cd` is the command used to change directories. It expects a relative or absolute path as an argument (we will explain the difference between paths shortly).

Go ahead and type the following command:

```
mkdir example_dir
```

This will create a directory in your home directory. To go into it, type `cd example_dir`. Now type `pwd`. You should see your path updated to something like `/home/csmajs/<your_CS_username>/example_dir`. To go back to the parent directory, type `cd ..`. Those two dots make up a special keyword that denotes the parent directory, so you don't have to type the full path out every time.

Now, type the following command:

```
cd /home/csmajs/<your_CS_username>/example_dir
```

Notice that you are in the same directory as before. The only difference was which type of path you passed in. In this case, `cd example_dir` is your relative path, since you are changing directories *relative to* your current directory. `cd /home/csmajs/<your_CS_username>/example_dir` is your absolute path, since you started your path name with the root directory `/`.

> Note: Your user root (home) directory is different from the server's/system's root directory. Your home directory is located in a special directory specifically made for CS majors (there is also a directory for faculty and grad students). The system's root directory, as described earlier, is the topmost directory of the whole server.

Go back to your home directory (`cd ~`). Another useful command is `ls`, which lists contents inside a directory. Go ahead and type that command now. You should see `example.txt` and `example_dir` as the only entries. Now type `ls -a`. You should now see a lot files preceded with the `.` character. These are hidden files that are used by the system for configurations. Now try `ls -al`. This gives you all the files (alongside the hidden files) in a format that shows user permissions and privileges for accessing content, as well as the date the file was created/modified. Out of all the contents, notice `.bashrc`. We will be editing this in the next section.

### The `bashrc` File

The `bashrc` file is a config file that is ran every time you login to Hammer. Go ahead and type `cat ~/.bashrc` to view its contents. You should see something like below:

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

This server uses a version of CentOS Linux, which is a Linux distribution that has the benefit of being extremely stable. The problem with this version of Linux is that in order to keep it stable, the CentOS developers do not update its tools to new versions very often. The version of the Git tool that is installed is not compatible with the online service GitHub, so you will need to enable an updated version of the tool for your account with the following command in your terminal:

```
$ source /opt/rh/devtoolset-6/enable
```

This will allow you to use a newer version of Git that is compatible with GitHub. **You will need to run this command every time you login to Hammer**, or alternatively, add it to your `.bashrc` using Emacs or Vim. 

> Note: If you are unfamiliar with command line editors, you can read these tutorials for [Vim](https://www.linux.com/tutorials/vim-101-beginners-guide-vim/) and [Emacs](http://www.jesshamrick.com/2012/09/10/absolute-beginners-guide-to-emacs/) to get started. Knowing at least one command line editor is extremely important since lots of new development is being done on cloud servers which do not have visual interfaces.

To make our lives easier for future labs, let's go ahead and add that source command into `.bashrc`. We will be using Vim for this example. Make sure you are in your home directory. Then, type `vim .bashrc`. This will open up `.bashrc` for editing in the Vim environment. Move down to the line that has `# User specific aliases and functions`. Next, hit `Shift-A`. This will move your cursor to the end of the line and put you in Insert mode. Go ahead and press Enter to go to the next line. Type in `source /opt/rh/devtoolset-6/enable`, then hit the Esc key, type `:wq` and hit Enter. `:` puts you in command mode, `w` saves the file, and `q` quits the editor (you can also type `:x`, where `x` combines the actions of `wq` into one character. Congrats! You have added your first user-specific command into `.bashrc`. Now, run `source .bashrc` to make sure the new devtools are loaded.

Another useful tool is aliasing. Aliasing works well when you have a complicated and/or elongated command to type in that you can shorten to something more recognizable. Think of passing a really long URL through [bit.ly](https://bitly.com). Let's look at a way to use aliasing.

`ls -al` is a nifty command to have, but wouldn't it be cool to type `la` instead? Let's go ahead and try that. Repeat the steps above with opening up `.bashrc` in Vim. Type the following command on the next line (after where you put the source commmand):

```
alias la="ls -al"
```

Save and exit the file, then run `source .bashrc`. Now when you can use `la`! Notice that this alias doesn't replace `ls -al`; you can still use the original form if you so choose.

For students with Linux and macOS machines: try making an alias in your local machine's `bashrc` file for logging into hammer. Something like this will work:

```
alias hammer="ssh <your_CS_username>@hammer.cs.ucr.edu"
```

### Extra Stuff

Note from Andrew: Not sure where to put the following note just yet. Maybe near the end of the lab?

> Note: You are not required to develop on the Hammer server for this course, and are encouraged to use your own development environment. However, you will need to validate that your code will run correctly on the Hammer server as we cannot account for differences in everyone's individual development environments. You are required to host your code on GitHub.
