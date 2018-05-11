# Step 3: Navigating the IFS

When you log in you are placed in a PASE shell and also into a directory in the IFS. Previously we used `pwd` to print the current working directory. You can also use the `ls` \(list\) command to display the contents of a directory.

Try the `ls` command for yourself and you should see something like the below.

```text
~% ls
~%
```

Nothing was listed because the directory is empty. Well, that’s only partially true. I just so happen to know there are "hidden" files in this directory. Hidden files start with a period and aren’t displayed by default. Most PASE commands have options that let you expand on what you want the command to do. The PASE commands delivered with the IBM i operating system lack what are called "man pages", or manual pages, the documentation for the command. Because of this we instead head to Google and search for " [ls command](http://linuxcommand.org/man_pages/ls1.html) ".

In the documentation we see the `-a` option, as shown below.

```text
       -a, --all
              do not hide entries starting with .
```

Now re-run the `ls` command with the `-a` option.

```text
~% ls -a
.          ..         .ssh       .zprofile
```

As you can see there are two hidden entries, `.ssh` is a hidden directory and `.zprofile` is a hidden file. You’ll also notice `.` and `..` \(a single and double period\). These are for when you want to use relative paths in your commands.

For example, if I wanted to change my current directly to that of my parent directory then I could issue the `cd` \(change directory\) command.

```text
% cd ..
/home%
```

Notice how this changes the current directory in my prompt to `/home`. Previously I was in my home directory \(i.e. `/home/USRZ8IGS`\) and it simply used `~`.

I can run the `ls` command again to see my home directory \(i.e. `USRZ8IGS`\) because it is a child directory of the `/home` directory.

```text
/home% ls
USRZ8IGS
```

Now try to list the contents of the root directory. The root is the single forward slash \(`/`\).

```text
~% ls /
QOpenSys  dev       home      opt       tmp       var
bin       etc       lib       sbin      usr
```

## Please proceed to the next step. {#_please_proceed_to_the_next_step}

