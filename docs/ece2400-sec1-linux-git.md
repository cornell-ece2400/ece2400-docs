
Section 1: Linux and Git
==========================================================================

This section serves as gentle introduction to the basics of using Linux
and Git on the `ecelinux` machines. The [corresponding
tutorial](ece2400-tut1-linux-git.md) provides significantly more detail
and background.

1. The `ecelinux` Machines
--------------------------------------------------------------------------

We will be using the `ecelinux` workstations and servers for all of the
programming assignments. The `ecelinux` machines all run the Red Hat
Enterprise Linux 7 operating system, and they all use an identical setup.
Linux is the operating system of choice for both cloud and IoT systems,
so becoming familiar with Linux will pay dividends beyond just this
course.

### Options For Using `ecelinux` machines

 - directly use workstations in 314 Phillips Linux Lab
 - log in remotely from 318 Phillips Windows Lab
 - log in remotely from your own laptop
 - in this discussion section, we will be directly logging into the
    workstations in the 314 Phillips ECE Linux Lab

### Logging Into a Workstatation

 - use your NetID and your standard NetID password
 - if you cannot log in, then it may be because you added the course
    since Thursday morning, we will be updating the access list often

### Joining The Zoom Screenshare

 - for the discussion section, we will be using screensharing via zoom
 - start zoom, choose from menubar: _Applications > Internet > Zoom_
 - click _"Join a Meeting"_
 - enter _"ece2400"_ in the text box
 - check _"Don't connect to Audio"_
 - check _"Turn off my video"_
 - click _"Join"_
 - click _"View Options"_ at the top
 - choose "Exit Full Screen"_
 - drag window over to the right-hand edge to make it take up the right
    half of the screen

2. Repl.it Online Development Environment
--------------------------------------------------------------------------

We will be using Repl.it to enable quickly exploring small C/C++ code
snippets. Repl.it is a free online service which supports writing,
compiling, and executing C/C++ programs completely online in your
browser.

 - start Firefox, choose from menubar: _Applications > Internet > Firefox_
 - drag window over to the left-hand edge to make it take up the
    left half of the screen

 - go to Repl.it here: https://repl.it
 - you can use Repl.it without an account, you can reate an account later
 - in the _"Search for a language"_ drop-down choose Python
 - the left panel is for entering code
 - the right panel displays the results of executing that code
 - enter the following Python program inspired by today's lecture

```python
def min(a,b):
  if a < b:
    c = a
  else:
    c = b
  return c

print(min(13,42))
```

 - remember, indentation is important in Python!
 - click _"run"_

 - again go back to Repl.it here: https://repl.it
 - in the _"Search for a language"_ drop-down choose C
 - you will see a default program
 - replace that program with this new program inspired by today's lecture

```c
#include <stdio.h>

int min( int a, int b )
{
  int c;
  if ( a < b )
    c = a;
  else
    c = b;
  return c;
}

int main()
{
  printf("%d",min(13,42));
  return 0;
}
```

 - click _"run"_
 - by the end of next week you will understand this program
 - students are encouraged to use Repl.it through the semester!

3. Linux Development Environment
--------------------------------------------------------------------------

While Repl.it is great for experimenting with small code snippets, it is
not suitable for serious system-level programming. So we will instead be
using the `ecelinux` workstations and servers which run the Red Hat
Enterprise Linux 7 operating system. The heart of the Linux operating
system is the Linux command line. This is a text-based console where you
can enter commands to interact with the operating system.

### Starting the Terminal

 - the terminal is where you can work at the Linux command line
 - choose from menubar: _Applications > Favorites > Terminal_

 - drag window over to the left-hand edge to make it take up the
    left half of the screen

### Hello World

We begin with the ubiquitous "Hello, World" example. To display the
message "Hello, World" we will use the `echo` command. The `echo` command
simply "echoes" its input to the console.

```bash
 % echo "Hello, World"
```

The string we provide to the echo command is called a _command line
argument_. We use command line arguments to tell commands what they
should operate on. Note that you do not need to enter `%` character. In a
tutorial like this, the `%` simply indicates what you should type at the
command line.

!!! question "To-Do On Your Own"

    Experiment with using the `echo` command to display different
    messages.

### Manual Pages

You can learn more about any Linux command by using the `man` command.
Try using this to learn more about the `echo` command.

```bash
 % man echo
```

You can use the up/down keys to scroll the manual one line at a time, the
space bar to scroll down one page at a time, and the `q` key to quit
viewing the manual.

### Create, View, and List Files

We can use the echo command and a feature called _command output
redirection_ to create simple text files. Command output redirection is
discussed more in the full tutorial. Command output redirection uses the
`>` operator to take the output from one command and "redirect" it to a
file. The following commands will create a new file named
`ece2400-tut1.txt` that simply contains the text "Computer Systems
Programming".

```bash
 % echo "Computer Systems Programming" > ece2400-tut1.txt
```

We can use the `cat` command to quickly display the contents of a file.

```bash
 % cat ece2400-tut1.txt
```

For larger files, `cat` will output the entire file to the console so it
may be hard to read the file as it streams past. We can use the `less`
command to show one screen-full of text at a time. You can use the
up/down keys to scroll the file one line at a time, the space bar to
scroll down one page at a time, and the `q` key to quit viewing the file.

```bash
 % less ece2400-tut1.txt
```

You can use the `ls` command to list the filenames of the files you have
created.

```bash
 % ls
```

We can provide command line options to the `ls` command to modify the
command’s behavior. For example, we can use the `-1` (i.e., a dash
followed by the number one) command line option to list one file per
line, and we can we can use the `-l` (i.e., a dash followed by the letter
l) command line option to provide a longer listing with more information
about each file.

!!! question "To-Do On Your Own"

    Create a new file named `ece2400-tut1-layer3.txt` which contains the
    third layer in the computing systems stack (i.e., programming
    language). Use `cat` and `less` to verify the file contents.

### Create, Change, and List Directories

Obviously, having all files in a single location would be hard to manage
effectively. We can use directories (also called folders) to logically
organize our files, just like one can use physical folders to organize
physical pieces of paper. The mechanism for organizing files and
directories is called the file system. When you first login to an
`ecelinux` machine, you will be in your home directory. This is your own
private space on the server that you can use to work on the programming
assignments and store your files. You can use the pwd command to print
the directory in which you are currently working, which is known as the
current working directory.

```bash
 % pwd
 /home/<netid>
```

You should see output similar to what is shown above, but instead of
`<netid>` it should show your actual NetID. The pwd command shows a
directory path. A directory path is a list of nested directory names; it
describes a "path" to get to a specific file or directory. So the above
path indicates that there is a toplevel directory named home that
contains a directory named `<netid>`. This is the directory path to your
home directory. As an aside, notice that Linux uses a forward slash (`/`)
to separate directories, while Windows uses a back slash (`\`) for the
same purpose.

We can use the `mkdir` command to make new directories. The following
command will make a new directory named `ece2400` within your home
directory.

```bash
 % mkdir ece2400
```

We can use the `cd` command to change our current working directory. The
following command will change the current working directory to be the
newly created `ece2400` directory, before displaying the current working
directory with the pwd command.

```bash
 % cd ece2400
 % pwd
 /home/<netid>/ece2400
```

Use the `mkdir`, `cd`, and `pwd` commands to make another directory.

```bash
 % mkdir tut1
 % cd tut1
 % pwd
 /home/<netid>/ece2400/tut1
```

We sometimes say that tut1 is a subdirectory or a child directory of the
`ece2400` directory. We might also say that the `ece2400` directory is
the parent directory of the `tut1` directory. Use the following command
to create a new file in this child directory.

```bash
 % cd /home/<netid>/ece2400/tut1
 % echo "Computer Systems Programming" > ece2400-tut1.txt
 % mkdir dirA
 % ls
```

You can use the `tree` command to visualize the directory layout and
where files are located:

```bash
 % cd ~/ece2400
 % tree
```

Note that the tilde character (`~`) is a shortcut which always refers to
your home directory. There are a few other very useful shortcuts. You can
use a single dot (`.`) to refer to the current working directory, and you
can use a double dot (`..`) to refer to the parent directory of the
current working directory.

```bash
 % cd ~/ece2400/tut1
 % cd ..
 % cd ..
 % pwd
```

!!! question "To-Do On Your Own"

    Experiment with creating additional directories and files within the
    `ece2400/tut1` subdirectory. Try using the `tree` command to display
    your newly created directory hierarchy.

### Copy, Move, and Remove Files and Directories

We can use the `cp` command to copy files. The first argument is the name
of the file you want to copy, and the second argument is the new name to
give to the copy. The following commands will make two copies of the
files we created in the previous section.

```bash
 % cd ~/ece2400/tut1
 % cp ece2400-tut1.txt ece2400-tut1-a.txt
 % cp ece2400-tut1.txt ece2400-tut1-b.txt
 % ls
```

Instead of copying we can also move a file with the `mv` command:

```bash
 % cd ~/ece2400/tut1
 % mv ece2400-tut1.txt ece2400-tut1-c.txt
 % ls
```

Finally, we can use the `rm` command to remove files.

```bash
 % cd ~/ece2400/tut1
 % ls
 % rm ece2400-tut1-a.txt
```

!!! question "To-Do On Your Own"

    Creating additional directories and files within the `ece2400/tut1`
    subdirectory, and then use the `cp`, `mv`, and `rm` commands to copy,
    move, and remove the newly created directories and files. Use the
    `ls` and `tree` commands to display your file and directory
    organization.

### Course Setup Script

Once you are logged into an ecelinux machine, you will need to setup the
working environment with the following command in order to work on the
course programming assignments.

```bash
 % source setup-ece2400.sh
```

### Editors

Students are free to use any text editor they want. We recommend `geany`.
You can start `geany` like this:

```bash
 % geany &
```

Try opening and editing the `ece2400-tut1.txt` file you created earlier.

4. Git Distributed Version Control System
--------------------------------------------------------------------------

In this course, we will be using Git as our revision control and source
code management system. We will be using GitHub for centralized online
repository hosting, and TravisCI for online continuous integration
testing. These tools will enable us to adopt an agile hardware
development methodology so your group can rapidly collaborate and iterate
on the design, verification, and evaluation of the assignments.

You can check to see if you have a GitHub account on `github.com` using
this link: `https://github.com/githubid` where `githubid` is your GitHub
username on `github.com`. If the above link does not work, then you do
not have an GitHub account on `github.com`. You will need to create one
here:

 - https://github.com/join

Your NetID makes a great GitHub username on github.com. Be sure to use
your Cornell University email address. Once your account is setup, please
make sure you set your full name so we can know who you are on GitHub.
