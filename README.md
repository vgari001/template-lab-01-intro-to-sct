# Intro to Software Construction Tooling

> Author(s): Andrew Lvovsky ([@borninla](https://github.com/borninla)) and Brian Crites ([@brrcrites](https://github.com/brrcrites))

Welcome to CS 100! For the first lab of the course, we are introducing the basics needed to get around the Linux environment. There are many reasons Linux is still used in industry today, ranging from its open-source nature to its excellent stability. There are various versions of Unix/Linux, known as distributions, which are built from a relatively small number of base kernels. In this course, we will be logging into and using a server provided by the university named `hammer`. In addition to learning Linux basics we will also (re-)introducing g++, CMake, Git and GitHub in this lab.

> Note: any text surrounded by angle brackets `< >` represent a portion of text that is specific to you and needs to be replaced. Make sure you replace that portion with what is requested in the lab description.

## SSH into Hammer 

You will need to log into the `hammer` server using a [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol) application.

If you are using a Linux or Mac computer, you can run the following command in the terminal:

```sh
ssh <your_CS_username>@hammer.cs.ucr.edu
```

If you are using a Windows computer, you will first need to install a program called PuTTY, which can be installed from [putty.org](http://www.putty.org/). When you open PuTTY, there will be a box for a “Host Name”, where you will input `<your_CS_username>@hammer.cs.ucr.edu`.

> For both of the above commands you should replace <your_CS_username> with the username provided to you by the CS department. If you do not yet have a CS account ask your TA to help you register a new account (some students will need to go through CS systems to get an account, your TA will advise you if this is the case).

You may be asked to exchange keys with the server (which you should allow). Next, you will be prompted for your password. You should note that when typing your password, no characters will be displayed even though you are still typing (this is a security measure).

> Note: You are not required to develop on the `hammer` server for this course, and are encouraged to use your own development environment. However, you will need to validate that your code will run correctly on the `hammer` server as we cannot account for differences in everyone's individual development environments. You are required to host your code on GitHub.

## The Linux File System

The Linux file system is similar to most other file systems. It is helpful to envision a file system like a tree. You have a root directory (node), denoted simply by `/` in Linux and Mac and typically `C:\` in windows, which has many children that are directories that all live in the root directory. Each of those directories can then hold other directores, which can hold other directories, etc. with as many levels as the user would like.

When you logged into `hammer`, you should've been placed into your user root directory (also called your home directory). To verify this, please type `pwd`, the command to "print working directory" or the current directory you are in. You should see the path `/home/csmajs/<your_CS_username>` (where `<your_CS_username>` is your CS username from before). If your path is not similar, type `cd ~` where the `~` character is a special character reserved by your terminal to represent your personal home directory. `cd` is the command used to "change directories" and expects a relative or absolute path as an argument (we will explain the difference between paths shortly).

One important thing to note at this point is that you are working on a server which can, and likely currently does, have multiple users connected to it at the same time. You can type the command `who` to see "who" is currently logged into the server. Each of these users has their own username, directories, processes, and resources on the server. This means that each user has a different `~` home directory that typing `cd ~` will take them to. This is an important concept to remember in order to develop programs which are portable and will work across users, but more on that later.

Go ahead and type the following command:

```sh
mkdir example_dir
```

This will create a new directory in your current directory, which (if you ran the last command) should be your home directory. To change into the new directory type `cd example_dir`. Then type `pwd` and you should see your path updated to something like `/home/csmajs/<your_CS_username>/example_dir`. To go back to the directory above, which should be your home direcotry, type `cd ..`. The two dots `..` represent a reserved directory that denotes the directory above your current directory, known as the parent. There is another reserved single dot directory `.` which represents the current working directory and we'll cover its usage later in this lab. Reserved directories like these make it easy to move up and down the directory structure in a relative manner without having to type the full path to the directory each time.

Now, type the following command:

```sh
cd /home/csmajs/<your_CS_username>/example_dir
```

Notice that you are in the same directory as before (you can verify by running `pwd`). The only difference was which type of path you passed in. In the prior case the `example_dir` in the command `cd example_dir` was a relative path, since you are changing directories *relative to* your current directory. The path `/home/csmajs/<your_CS_username>/exampale_dir` used in `cd /home/csmajs/<your_CS_username>/example_dir` is an absolute path, since it starts with the root directory `/`. All paths which start from the root directory `/` are absolute paths, otherwise the path is relative, although in both cases the path could be valid or invalid.

> Note: Your user root (home) directory is different from the server's root directory. Your home directory is attached to your account and resides as a directory specifically created to hold your files (directories like these are automatically created when new users are added). The system's root directory is the highest (or lowest depending on your view) path on the server and has no parent directory (if you `cd ..` from `/` it will send you back to `/`).

Go back to your home directory (`cd ~`) and type the command `ls` which lists the contents of a directory. You should see the directory `example_dir` we created before as the only entry. Now type `ls -a` and you should see additional "hidden" files which start with a dot (`.`). Any file (even ones you create) that start with a dot are considered hidden files by the system and won't be shown with a normal `ls` command. These files are typically system or configuration files which are used by various systems, such as version control or build systems, or Linux itself, such as `.` and `..`. 

Now that we've demonstrated how to create and traverse directories, let's go ahead and delete `example_dir`. Make sure you are in your home directory, then type:

```sh
rm -rf example_dir
```

`rm` is the command used to "remove" files or directories. If you have a regular file you can enter `rm <filename>` without any flags. However, since we are dealing with a directory we need to add the extra `-rf` options. `r` recursively removes files and directories within the directory you are removing and 'f' ignores nonexistent files and doesn't prompt the user to confirm each deletion. That said, with great power comes great responsibility. Use `rm -rf` only when necessary. The last thing you want is to accidently delete important files or directories.

## The `bashrc` File

Another example of a hidden file used by a system is the `.bashrc` file, which you should see when running `ls -a` from your home directory (`~`). The `bashrc` file is a config file that is executed every time your terminal loads such as when you login to `hammer` over SSH. Go ahead and type `cat ~/.bashrc` to view its contents. You should see something like below:

```sh
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

```sh
$ source /opt/rh/devtoolset-6/enable
```

This will allow you to use a newer version of Git that is compatible with GitHub as well as give you access to a compiler which has the C++11 standard which we will need for this class. You would normally need to run this command every time you login to `hammer`, which is not ideal. Instead, you will modify the `.bashrc` file to run this command for you every time your open a terminal, which will require you to learn the basics of a command line text editor such as Vim or Emacs.

> Note: If you are unfamiliar with command line editors, you should read these tutorials for [Vim](https://www.linux.com/tutorials/vim-101-beginners-guide-vim/) or [Emacs](http://www.jesshamrick.com/2012/09/10/absolute-beginners-guide-to-emacs/) to get started. Knowing at least one command line editor is extremely important since most servers (especially cloud-based server instances) do not have visual interfaces, so the only way to make changes to files on them is through a command line editor.

We will be using Vim here to add the necessary code to the `.bashrc` file. Make sure you are in your home directory and type `vim .bashrc` to open the `.bashrc` file for editing in Vim. Vim is designed to allow programmers to work quickly, so it tries to keep you hands on the home row of the keyboard as much as possible. It uses the `j` key to move down, the `k` key to move up, the `l` key to move right, and the `h` key to move left. Move down to the line that has `# User specific aliases and functions` using these keys. Next, hit `Shift-A`. This will move your cursor to the end of the line and put you in Insert mode. Vim has multiple modes, the major ones of which are normal mode which takes key commands as as `hjkl` to move or `Shift-A`, and insertion mode, which allows you to type text like you would in a word document (there is also a visual mode which is very useful, but we won't cover it here). You can tell if you are in insert mode by the **-- INSERT --** at the bottom of your window and can use the Esc key to go back to normal mode. 

Go ahead and press Enter while in Insert mode to go to (and create) the next line. Type in `source /opt/rh/devtoolset-6/enable`, then hit the Esc key to go back to normal mode. Type `:` to enter a command sequence followed by `wq` which represent writing the file (`w`) and quiting the program (`q`) and hit Enter. The `:` puts you in command mode which is very powerful in Vim and will let you do everything from [replace all behavior](https://www.linux.com/tutorials/vim-tips-basics-search-and-replace/) to [running bash commands without exiting Vim](https://www.linux.com/tutorials/vim-tips-working-external-commands/). Congrats! You have added your first user-specific command into `.bashrc`. Now, run `source .bashrc` to make sure the new devtools are loaded (you need to do this to reload the file, otherwise you would need to quit `hammer` and log back in for the `.bashrc` file to load).

Another useful tool is aliasing. Aliasing works well when you have a complicated and/or elongated command to type in that you can shorten to something more recognizable. Think of passing a really long URL through [bit.ly](https://bitly.com). Let's look at a way to use aliasing.

`ls -al` is a nifty command to have, but wouldn't it be cool to type something shorter like `la` instead? Let's go ahead and try that. Repeat the steps above with opening up `.bashrc` in Vim. Type the following command on the next line (after where you put the source commmand):

```sh
alias la="ls -al"
```

Save and exit the file, then run `source .bashrc`. Now you can use `la`! Notice that this alias doesn't replace `ls -al`; you can still use the original form if you so choose.

For students with Linux and macOS machines: try making an alias in your local machine's `.bashrc` file for logging into `hammer`. Something like this will work:

```sh
alias hammer="ssh <your_CS_username>@hammer.cs.ucr.edu"
```

## Command Line Compilation

GCC is the GNU project C Compiler and is one of the most used compilers for C code (macOS previously used GCC but not uses another compiler called Clang). It typically also comes with G++ which is a similar compiler for C++ code. The `g++` command invokes this compiler from the command line. To demonstrate how it works, go ahead and write a simple `hello world!` program using the command line editor of your choice (save the file as `main.cpp`):

```c++
#include <iostream>

using namespace std;

int main()
{
    cout << "hello world!" << endl;
    return 0;
}
```

Once finished make sure to save and quit out of your editor and then type the following command in the terminal:

```sh
g++ -std=c++11 main.cpp
```

> Note: Most shells support autocompletion when typing out commands, and is useful when you have a command with many arguments to type out. For example, start typing `g++ -std=c++11 ma`. After typing `a`, press the Tab key. Bash (the shell you are currently using in `hammer`) should autocomplete to `main.cpp`.

If successful, you shouldn't see any output (if not successful, fix your errors until you are able to compile your program). A quick `ls` will show that a new file named `a.out` was generated, which is the executable generated from your program. You execute this executable (also known as a binary) with `./a.out`. Note that the `.` in this case is the current directory and is required [primarily for security reasons](https://stackoverflow.com/questions/6331075/why-do-you-need-dot-slash-before-executable-or-script-name-to-run-it-in-bas). When you execute your program you should see "hello world!" printed out to the terminal.

> Note: The above command will work without the `-std=c++11` flag. By default g++ will compile to the C++98 standard which does not have a number of features we will need in this course. You will need to use this flag to enable the C++11 standard which we will use in this course.

Most of the time you want to give your program a recognizable name. Adding the `-o` "output" flag followed by a name and the executable will output with that name. Go ahead and run `rm a.out` to delete the old executable, then run the following command:

```sh
g++ -std=c++11 -o hello_world main.cpp
```

You have now created an executable called `hello_world` and can execute it by typing `./hello_world`.

When developing larger programs in object-oriented languages (like you will in this class), it is common to break up work into multiple source and header files (in C++'s case, `.cpp` and `.hpp` files respectively). The g++ compiler allows you to compile multiple files into a single executable.

Before we write our files, lets create some directories to seperate our files and make things easier to find. Create a `src` directory and a `header` directory where we will put the respective source and header files. We will be creating a simple Rectangle class which has a height and width and calculates the rectangle's area. Go ahead and make a header file called `rectangle.hpp` in the `header` directory, and add the following code:

```c++
#ifndef RECTANGLE_HPP
#define RECTANGLE_HPP

class Rectangle {
    private:
        int width;
        int height;
    public:
        void set_width(int w);
        void set_height(int h);
        int area();
};

#endif /* RECTANGLE_HPP */
```

> The `#ifndef`, `#define`, and `#endif` are known as [sharp guards or include guards](https://www.cprogramming.com/tutorial/cpreprocessor.html) and cause the compiler to only include this file once even when its referenced multiple times. It is a good idea to add these to the top of all your header files, and we will cover them more in a future lab.

Also create a source file called `rectangle.cpp` in the `src` directory, and add the following code:

```c++
#include "../header/rectangle.hpp"

void Rectangle::set_width(int w) {
    this->width = w;
}

void Rectangle::set_height(int h) {
    this->height = h;
}

int Rectangle::area() {
    return this->width * this->height;
}
```

Finally, overwrite your current `main.cpp` with following code:

```c++
#include <iostream>
#include "../header/rectangle.hpp"

using namespace std;

int main()
{
    Rectangle rect;
    rect.set_width(3);
    rect.set_height(4);
    cout << "Rectangle area: " << rect.area() << endl;
    return 0;
}
```

The `main.cpp` file was not created in the `src` directory so we should move it there. You can use the command `mv <source> <destination>` to move a file from `<source>` to `<destination>` where the `<source>` is a file and the `<destination>` is a file or folder. From your home directory where `main.cpp` should be use `mv main.cpp src/` to move `main.cpp` into the `src` directory. If you added a filename after `src/` in the destination part of the move command, it would also rename the file. If its omitted, it will use the source files name (which is fine in this case). The `mv` move command is used for both moving files and renaming them. 

We are now ready to compile and run! Go ahead and run the following command (notice that the `g++` command can take relative paths, so the command below is being run from your home directory):

```sh
g++ -std=c++11 -o area_calculator src/main.cpp src/rectangle.cpp
```

Notice that we didn't include the header file `rectangle.hpp` as an argument. The `#include "rectangle.hpp"` within `rectangle.cpp` tells the compiler to include the header for us (and is why the include guards are necessary). Nice! Go ahead and run `./area_calculator`. You should see `Rectangle area: 12` as output.

### Make

Now we briefly introduce Make, which is a GNU project build automation tool. Make is essentially a scripting language for building executables from source code. It works by reading a `Makefile` (that is the required file name) which is a text file that tells Make how to build the target program. The `Makefile` is made up of rules that look like the following:

```make
target: dependencies ...
    commands
    ...
```

Let's go ahead and create a `Makefile` for our program above. Add the following rule into the `Makefile`:

```make
area_calculator: src/main.cpp src/rectangle.cpp
    g++ -o area_calculator src/main.cpp src/rectangle.cpp
```

Now go ahead and run `make` in your terminal and you should see the rule's command displayed as output. Using `make` allows you to save lots of time typing out compilations commands so you don't need to keep entering `g++ -o area_calculator main.cpp rectangle.cpp` over and over again when making edits to the source files. It also allows you to create multiple executables from a single command. However, we won't be using Make directly in this course but instead a more powerful system.

### CMake

[CMake](https://cmake.org/) is a build system built on top of GNU's `make` and supports some more advanced features. The CMake system looks for a `CMakeLists.txt` file (again this is the required file name) in order to know what to build. Start by creating the following `CMakeLists.txt` file:

```cmake
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

ADD_EXECUTABLE(area_calculator
    src/main.cpp
    src/rectangle.cpp
)
```

The first function, `CMAKE_MINIMUM_REQUIRED`, sets the minimum version of CMake that can be used to compile this program. The function `ADD_EXECUTABLE` tells CMake to create a new executable named after the first parameter in that function, in this case `area_calculator`. We then list all the `.cpp` files which need to be included in that executable. Earlier, we mentioned that CMake is built on top of `make`. To be more specific, it generates really good Makefiles. Run the following command from the terminal in order to generate a new Makefile to compile your program:

```sh
$ cmake3 .
```

This command envokes the CMake build system in the local directory (where our CMakeLists.txt file is located). 

> Note: **Make sure you use the `cmake3` command and not just `cmake`**. Hammer has two versions of CMake installed because of the tool sourcing we did at the beginning of the lab, and if you do not use the `cmake3` command you will get an error. If you are developing on your own machine, you will likely just use the `cmake` command since you will only have one version of CMake installed. 

CMake should now have generated a new `Makefile`, so execute the `Makefile` with the `make` command. You should see a nicely-designed build percentage which will generate a new `area_calculator` executable.

```sh
$ make
Scanning dependencies of target area_calculator
[ 33%] Building CXX object CMakeFiles/area_calculator.dir/src/main.cpp.o
[ 66%] Building CXX object CMakeFiles/area_calculator.dir/src/rectangle.cpp.o
[100%] Linking CXX executable area_calculator
[100%] Built target area_calculator
```

As you may have guessed CMake and Make both allow you to generate multiple executables. In this class we will use this functionality to build executables for the regular program and for testing. Lets try creating another executable by creating another main which runs a slightly different program. Create a file with the following code named `new_main.cpp` in the `src` directory:

```c++
#include <iostream>
#include "../header/rectangle.hpp"

using namespace std;

int main()
{
    Rectangle rect;
    rect.set_width(15);
    rect.set_height(30);
    cout << "Rectangle area: " << rect.area() << endl;
    return 0;
}
```

Now add the following snippet of code to the `CMakeLists.txt` file you created previously:

```cmake
ADD_EXECUTABLE(bigger_area_calculator
    src/new_main.cpp
    src/rectangle.cpp
)
```

Now if you run `cmake3 .` and `make` you will see two executables generated `area_calculator` and `bigger_area_calculator`. If you want to only build one of the executables (for example if you updated only one of the mains) then you can invoke `make` with the name of the executable you want it to build, `make area_calculator` for example, and it will only build that one executable.

## Intro to Git and Github

### Git Config

Git is a local program for performing version control which is usually paired with a remote Git server for saving code off site. GitHub is a web-based Git repository which your local Git program is capable of interfacing with. Git and GitHub are therefore two seperate systems and Git needs to be configured correctly in order for your code changes to be tracked and attributed correctly on GitHub. You should run the following commands on any new system you are committing from before you start working (these can be run from any directory and only have to be run once per system):

```sh
git config --global user.name "<github-username>"
git config --global user.email <github-registered-email>
```

GitHub will use the email that you configure with your Git client to track which users are making which changes. This means that you'll need to use the email address you've registered with GitHub in the above configurations (otherwise you may see commits from an anonymous user). In this course we look at commits and who made them to make sure partners in the projects and labs are contributing equally, so having misattributed commits may lead to point deductions. If you have a few which are misattribued this is not an issue but you should configure your client quickly after you notice the problem to correct the issue.

### Git Init & Clone

New Git repositories can be created either locally using the Git client or through GitHub directly. Make sure you are back at your home directory (`cd ~`) and run the following commands on the terminal in `hammer`:

```sh
mkdir lab-01
cd lab-01
```

Then to initialize that directory as a Git project, use the following Git command:

```sh
git init
```

This will create a hidden `.git` repository (which you can see with `ls -a`) that holds all the information that Git uses to keep track of your files and changes. The folder is hidden because it begins with a period (recall earlier in the lab) and is typically not modified by users directly.

However, you use a different method if you want to receive a copy of an already existing repository, which you will do for all of the labs and assignments in this course. Rather than initializing a new repository, you will make a “clone” of a repository that already exists. Start by moving out of the Git project you just created and removing that directory:

```sh
cd ..
rm -rf lab-01
```

Now, go to the upper right of this page and click the “Clone or download” dropdown. A box will appear that should say “Clone with HTTPS” (if it says “Clone with SSH”, then click the small blue text to the right that says “Use HTTPS”). The box should look like the following, being careful to make sure it says "Clone with HTTPS".

![Image of clone popup set to HTTPS](https://github.com/cs100/template-lab-01-git-github/blob/master/images/https-clone.png?raw=true)

Copy the link in the box below, this is the GitHub repository url which you will use to clone that repository. Now, run the following command:

```sh
git clone <github-url>
```

Make sure to replace the above `<github-url>` with the url that you copied from the “Clone or download” box. This will create a new folder named `lab-01-intro-to-sct-...` with some additional text based on your username/groupname. This new directory is a copy of the GitHub repository, and is already initialized as a Git project. Move into this new directory and we can begin modifying it.

> Note: README.md files are special in GitHub repositories. The contents of the README.md file in the repository's root will actually be rendered along with a list of files for anyone who visits the repository. The hash (#) at the beginning of the line is part of GitHub Markdown which specifies that this line should be a title. [You can read more about GitHub Markdown here](https://guides.github.com/features/mastering-markdown/).

### Git Status, Add & Commit

Remember our program and its source/header files from earlier? Go ahead and move all of those files into the new `lab-01-intro-to-sct-...` folder.

Git doesn’t automatically keep track of new files for us. Instead, we have to tell Git to start tracking these new files. Run the following command:

```sh
git status
```

The `status` command lists the current status of your Git repository, mostly showing whcih files have changes. In the output, there is a section labeled "untracked files." Notice the files in that section. This means that Git knows these files exist, but isn’t currently keeping track of changes to them. We want to keep track of all the `.cpp` `.hpp` files and `CMakeLists.txt`, but not the `area_calculator` file since that should be recompiled to run correctly on different machines. It is important to note that Git does not automatically save changes to your files either locally or on GitHub. When you have made a set of changes that you want to save, you will have to use the commands we are going to introduce below so you will use these commands very often.

We don’t want Git to continue to tell us that `area_calculator` is untracked, but luckily Git has a solution for this problem. You can create a file called `.gitignore` that will contain a list of all of the files that you want Git to ignore when it tells you what is/isn’t tracked and modified. Create a file named `.gitignore` and add `area_calculator` to it. Now run `git status` again and take a look at the output. Notice that `area_calculator` is no longer listed, only the `.cpp` `.hpp`, `CMakeLists.txt` and the new `.gitignore` files are listed as untracked. Now, we can add all of these files to our project with the following commands:

```sh
git add header/rectangle.hpp
git add src/rectangle.cpp
git add src/main.cpp
git add src/new_main.cpp
git add .gitignore
```

> Note: Do not add executables, object files, or temporary files which are re-generated during compilation to your git repo, only add source files and other necessary files for your submission (these are listed in the assignment specifications). Tracking executables uses LOTS of disk space and they are unlikely to work on other peoples machines. **If we see these files in your Git repos, your grade on the assignment will be docked 20%**. You should use a `.gitignore` file so that they don’t appear in your Git status or accidentally get added to your repository.

Now, when we run Git status, there is a section labeled "Changes to be committed" with the files that were just added underneath it. This means that Git now thinks these files are part of the project, and will begin to track changes to it.

Whenever we finish a task in our repo, we "commit" our changes. This tells Git to save the current state of the repo, made up of all the files we’ve added, so that we can come back to it later if we need to. Commit your changes using the command:

```sh
git commit -m "Add initial files”
```

Every commit needs a "commit message" that describes what changes were made in the repo. Writing clear, succinct, informative commit messages is one of the keys to using Git effectively. In this case we passed the `-m` flag to Git so we could write the commit message in the command line. If we did not pass a flag, then Git would have opened the Vim editor for us to type a longer commit message which is useful when you are commiting more major changes which require more explanation.

That was not a very informative commit message, so lets edit it to be something more informative. Anytime you need to modify the last commit that you made, you need to amend it, which is done through the following command:

```sh
git commit --amend
```

Running this allows you to edit the last commit that you made, including which files were included in that commit. Anything that you have done a `git add` to before running `git commit --amend` will be added to the last commit in addition to allowing you to edit the commit message.

> Note: the --amend flag will let you add anything to the previous commit, even if it's not a good idea. Because of this you should use it carefully and for situations where you have forgotten a minor change (README, comment, better commit message) or your last commit did not actually function correctly and you need to “patch” it.

We didn’t write an informative commit message, so we should modify it to be something more useful. Run the `git commit --amend` command. You should see something like the following open in either Vim or Emacs.

```
My first commit
  
# Please enter the commit message for your changes. Lines starting
# with ‘#' will be ignored, and an empty message aborts the commit.
#
# On branch brrcrites/lab-01
# Changes to be committed:
#   added:   header/rectangle.hpp
#   added:   src/rectangle.cpp
#   added:   src/main.cpp
#   added:   .gitignore
#
# Changes not staged for commit:
#
# Untracked files:
#
```

While you can write any message that you want here, GitHub will do some automatic formatting based on past good practices for writing commit messages that you should adhere to. 

* The first line will be formatted as a subject line, and cut off at 50 characters. You should therefore pick a concise subject message that is < 50 characters long 
* You should separate the first line from the rest of your body text with a blank line
* The body of your message (anything else you add after the subject but before the commented lines) should describe what changes occurred and why, rather than how you did it. This is often formatted as a bulleted list using a * as the bullet.

Following these rules will make your commits nicely formatted on GitHub and easier to understand and process. Lets update our commit message to the following:

```
Add rectangle area program

* Added Rectangle class files and main file that calculates the rectangle's area
  
# Please enter the commit message for your changes. Lines starting
# with ‘#' will be ignored, and an empty message aborts the commit.
#
# On branch brrcrites/lab-01
# Changes to be committed:
#   added:   header/rectangle.hpp
#   added:   src/rectangle.cpp
#   added:   src/main.cpp
#   added:   .gitignore
#
# Changes not staged for commit:
#
# Untracked files:
#
```

After editing the file with the above message, exit your editor and the commit should update.

> Note: The -m flag should only be used when writing very short commit messages, and otherwise you should use the interactive mode to have both a subject and commit body. For some more tips on writing effective git commit messages, read [this blog post](https://chris.beams.io/posts/git-commit/).

Let's make one more commit so we'll have something to play with. Update your `main.cpp` to calculate one more rectangle's area:

```c++
#include <iostream>
#include "../header/rectangle.hpp"

using namespace std;

int main()
{
    Rectangle rect1, rect2;
    rect1.set_width(3);
    rect1.set_height(4);

    rect2.set_width(4);
    rect2.set_height(2);

    cout << "Rectangle 1 area: " << rect1.area() << endl;
    cout << "Rectangle 2 area: " << rect2.area() << endl;

    return 0;
}
```

Now run:

```sh
git commit -m "Add one more rectangle and compute its area”
```

Uh oh! We got an error message saying: "no changes added to the commit".

Every time you modify a file, you must explicitly tell Git to add the file again if you want that file included in the commit. This is because sometimes programmers want to commit only some of the modified files. 

Run `git status` to see the files that are currently being tracked. We can add the changes to the `main.cpp` file, and verify it is added, with:

```sh
$ git add main.cpp
$ git status
```

We can then commit the changes using:

```sh
$ git commit -m "Add one more rectangle and compute its area”
```

### Git Push & Pull

While git is a version control system (VCS), GitHub is a remote repository which is an important distinction for two reasons. The first is that up until now all the work you’ve done has only been saved locally, so if there is a problem with your computer you would have no backup and therefore no way to recover the files. The second is that because all the changes are local, there is no way for people collaborating with you to see or receive your changes. Go to your GitHub repository for this lab, and you should see that none of the work you've done is listed.

Since we cloned the remote repository from GitHub directly, our local repository is already associated with a remote repository (usually referred to as “remote” or “upstream”). In order to send the changes we’ve made locally to GitHub, we just need to “push” them up to the server (do this now).

```sh
$ git push
```

This will push all the commits you've made since your last push assuming there haven't been any changes to the remote GitHub repo that your local Git doesn't know about. If there have been you will need to "pull" and "merge" those changes into your local repository, but we will cover those steps in a future lab when we cover Git and GitHub in more depth.

## Submission

None of the labs or assignments for this course require direct submissions but will be graded based on your GitHub repositories. Projects will be graded based on specific tags (we will explain tags in a future lab) and labs will be graded based on your last GitHub commit and a demonstration of your code to your TA. Since you have just pushed your code to GitHub you now need to demonstrate that you have completed the lab to the TA.
