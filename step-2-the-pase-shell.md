# The Shell {#the-shell}

You access PASE using what’s called a _shell_. The [PASE](https://kti.news/pase-v73\) shell is a lot like a 5250 Telnet session; it’s a program that takes your commands from the keyboard and hands them to the operating system to perform their associated action. It is also known as a CLI \(Command Line Interface\).

By default PASE uses the Korn shell \(aka `ksh`\). One way you can enter into a PASE Korn shell is via `CALL QP2TERM` from a 5250 Telnet session, as shown below.

![](https://litmis.gitbooks.io/pase-intro/content/assets/pase_call_qp2term1.png)

Figure 1.

**CALL QP2TERM from a 5250 Telnet Session**

The `pwd` command has been keyed in and the enter key pressed. Under the covers the `pwd` C program is invoked, does its thing, and returns output back to your shell’s screen.

## QP2TERM and Qshell {#_qp2term_and_qshell}

If you’ve been on the IBM i for any amount of time you’ve probably been exposed to Qshell. You can start a Qshell session by typing `QSH`. You’ll notice Qshell looks **very** similar to `CALL QP2TERM`.

![](https://litmis.gitbooks.io/pase-intro/content/assets/qshell_strqsh.png)

The primary difference between these two environments is that Qshell is based on the ILE environment and QP2TERM is based on AIX. For 99% of what you do you will stay away from Qshell because it doesn’t operate correctly in certain circumstances. The few scenarios you will use it involve starting a shell script from a 5250 session; but it will in turn enter into a real PASE shell.

```
SBMJOB CMD(QSH CMD('/path/to/script.sh parm1 parm2'))
```

A significant amount of more information concerning PASE shells can be found on the [YiPs site](https://kti.news/yips-pase-shell\) where Tony Cairns \(IBM’er at Rochester\) goes deep and wide on a variety of PASE topics.

## A Better Shell {#_a_better_shell}

Ok, we now know Qshell should be used in limited fashion and that getting to the Korn shell via `CALL QP2TERM` is better, but it still isn’t the best.

Instead what you should be using is a more feature rich shell, like `bash`. The Bourne Again Shell \(bash\) is now included with [5733OPS option 7](http://bit.ly/dw-5733ops-tools).

**Why is Bash better?**

* **Tab key to complete.** Instead of fully typing a directory or file name you can hit the tab key and it will auto complete it.

* **Short-cut keys.** For example, Ctrl+A and Ctrl+E go to the beginning and end of the current line, respectively.

* **Custom Prompt.** Bash allows you to customize your prompt to include things like the current directory and the current user, e.g. `[aaron@SPACES]/home/aaron/git$`

The other thing you need to know is that, while Bash works in QP2TERM, it is fairly crippled there. For example, the tab-to-complete and short-cut keys don’t work in QP2TERM because it isn’t what is called a teletypewriter \(TTY\) terminal. For that matter, a lot of basic functionality in ksh or any other Unix-style shell is unavailable without a TTY terminal. To get a TTY terminal we need to start the `*SSHD` \(SSH Daemon\)\[[1](https://litmis.gitbooks.io/pase-intro/content/step-2-the-shell.html#_footnote_1\)] server and then use a client-side program \(on the desktop\) to connect to a bash shell on IBM i.

Once the `*SSHD` TCP server is started you can then connect to it using a terminal client, as shown below on Windows using [Git Bash](https://kti.news/gitbash-for-windows).

![](https://litmis.gitbooks.io/pase-intro/content/assets/gitbash_login_windows7.png)

Or on a Mac using [iTerm2](https://www.iterm2.com).

![](https://litmis.gitbooks.io/pase-intro/content/assets/iterm_bash.png)

Stay tuned for how you’ll access the PASE shell for this lab.

## Please proceed to the next step. {#_please_proceed_to_the_next_step}

---



