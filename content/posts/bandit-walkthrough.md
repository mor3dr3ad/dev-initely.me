+++
title = "Bandit Complete Walkthrough"
date = 2025-03-27T14:43:00+01:00
draft = false
+++

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2025-03-27 Thu 14:43]</span></span>

The Bandit wargame is aimed at absolute beginners. It will teach the basics needed to be able to play other wargames. If you notice something essential is missing or have ideas for new levels, please let us know!

Note for beginners
This game, like most other games, is organised in levels. You start at Level 0 and try to “beat” or “finish” it. Finishing a level results in information on how to start the next level. The pages on this website for “Level &lt;X&gt;” contain information on how to start level X from the previous level. E.g. The page for Level 1 has information on how to gain access from Level 0 to Level 1. All levels in this game have a page on this website, and they are all linked to from the sidemenu on the left of this page.

You will encounter many situations in which you have no idea what you are supposed to do. Don’t panic! Don’t give up! The purpose of this game is for you to learn the basics. Part of learning the basics, is reading a lot of new information. If you’ve never used the command line before, a good first read is this introduction to user commands.

There are several things you can try when you are unsure how to continue:

-   First, if you know a command, but don’t know how to use it, try the manual (man page) by entering man &lt;command&gt;. For example, man ls to learn about the “ls” command. The “man” command also has a manual, try it! When using man, press q to quit (you can also use / and n and N to search).
-   Second, if there is no man page, the command might be a shell built-in. In that case use the “help &lt;X&gt;” command. E.g. help cd
-   Also, your favorite search-engine is your friend. Learn how to use it! I recommend Google.
-   Lastly, if you are still stuck, you can join us via chat
-   You’re ready to start! Begin with Level 0, linked at the left of this page. Good luck!

Note for VMs: You may fail to connect to overthewire.org via SSH with a “broken pipe error” when the network adapter for the VM is configured to use NAT mode. Adding the setting IPQoS throughput to /etc/ssh/ssh_config should resolve the issue. If this does not solve your issue, the only option then is to change the adapter to Bridged mode

-   Level 0: _ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If_
-   Level 1: _263JGJPfgU6LtdEvgfWU1XP5yac29mFx_
-   Level 2: _MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx_
-   Level 3: _2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ_
-   Level 4:
    -   The `file` command gives us the type of data of the file, e.g. ELF which is not human-readable. The most common data encodings that are human-readable are `ASCII` and `Unicode`
    -   So we can just do `file ./*` do see which file is human-readable and then cat the content giving us the password.
    -   _4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw_
-   Level 5 -&gt; 6:
    -   We are looking for a file that is `human-readable`, `1033 bytes` and `not executable`
    -   So we need to use some combination of `file`, `du`, `ls`
    -   A couple of helpful commands:
        -   `du -b -a` -&gt; gives us the byte size for all files, we can feed this into `grep 1033` to get all files with the bytesize of 1033
    -   `find . -type f -size 1033c ! -executable -exec file '{}' \; | grep ASCII`
        -   find all files
        -   with size 1033
        -   which are non-executable
        -   `exec file '{}'` executes the file command to get the file type for all results - we then filter using `grep ASCII`
    -   _HWasnPhtq9AVKe0dmk45nxy20cvUa6EG_
-   Level 6 -&gt; 7
    -   find has flags for -user and -group
    -   `find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null`
    -   _morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj_
-   Level 7 -&gt; 8
    -   _dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc_
-   Level 8 -&gt; 9
    -   `sort data.txt | uniq -u`
    -   _4CKMh1JI91bUIZZPXDqGanal4xvAg0JM_
-   Level 9 -&gt; 10
    -   `strings data.txt | grep =`
    -   _FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey_
-   Level 10 -&gt; 11
    -   `base64 -d data.txt`
    -   _dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr_
-   Level 11 -&gt; 12
    -   `cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'`
    -   _7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4_
-   Level 12 -&gt; 13
    -   The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed.
    -   First we need to reverse hexdump `xxd -r data.txt`
    -   Then look at the first hex values, e.g. `1f 8b` which is a gzip compressed file. rename `mv data.txt data.gz`, unzip and proceed using `gzip -d`, `bzip -d` and `tar -xvf` until human-readable file.
    -   _FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn_
-   Level 13 -&gt; 14
    -   The password for the next level is stored in `/etc/bandit_pass/bandit14` and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on
    -   _MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS_
-   Level 14 -&gt; 15
    -   The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.
    -   `echo 'password' | nc localhost 30000`
    -   _8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo_
-   Level 15 -&gt; 16
    -   The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL/TLS encryption.
    -   `echo 'password' | ncat --ssl localhost 30001`
    -   _kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx_
-   Level 16 -&gt; 17
    -   The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.
    -   Scan open ports `nmap -p 31000-32000 localhost`
    -   Find out which ports run ssl: `nmap -p 31000-32000 -sV localhost`
    -   Try the ports via `echo 'password' | ncat --ssl localhost 31790`
    -   Correct -&gt; ssh creds
-   Level 17 -&gt; 18
    -   There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new
    -   find the password via `diff password.old password.new`
    -   x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
-   Level 18 -&gt; 19
    -   The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.
    -   so we get logged out immediately - let's then use ssh to run a command before logging out: `sudo ssh -i bandit17 bandit17@bandit.labs.overtheiwre.org "cat readme"`
    -   cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
-   Level 19 -&gt; 20
    -   Read the bandit20 pass by using the setuid binary
    -   _0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO_
-   Level 20 -&gt; 21
    -   There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).
    -   First, we need to serve the password on a specific port; we can do so via netcat: `echo 'password' | nc -l -p1234 &`. The `&` sign puts the command in the puts the command in the background. (we can also use 'CTRL + Z' and bg)
    -   Then we can connect to the port using the binary given
    -   _EeoULMCra2q0dSkYj561DX7s1CpBuOBt_
-   Level 21 -&gt; 22
    -   A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in _etc/cron.d_ for the configuration and see what command is being executed.
    -   Check cronjob under `/etc/cron.d/cronjob_bandit22`
    -   Then check the contents under `/usr/bin/cronjob_bandit22.sh`
    -   _tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q_
-   Level 22 -&gt; 23
    -   Check `/usr/bin/cronjob_bandit23.sh`
    -   It runs a bash script copying the pass to an unknown temp folder. But (!) the tmp folder gets created by md5sum'ming the user name. We can copy the bash script and hardcode bandit23 instead of 'whoami'.
    -   _0Zf11ioIjMVN551jX3CmStKLYqjk54Ga_
-   Level 23 -&gt; 24
    -   A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in `/etc/cron.d/` for the configuration and see what command is being executed. NOTE: this will require you to write your own shell script.
    -   check the cronjob under `/usr/bin/cronjob_bandit24.sh`
        ```bash
        #!/bin/bash

        myname=$(whoami)

        cd /var/spool/$myname/foo
        echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
        for i in * .*;
        do
            if [ "$i" != "." -a "$i" != ".." ];
            then
                echo "Handling $i"
                owner="$(stat --format "%U" ./$i)"
                if [ "${owner}" = "bandit23" ]; then
                    timeout -s 9 60 ./$i
                fi
                rm -f ./$i
            fi
        done
        ```
    -   The script will execute **all** scripts in the folder `/var/spool/bandit24/foo/`. So what we should do is place a file in the folder above that copies the password to a folder of our choosing.
    -   First, create a hard-to-guess temporary folder via `mktemp -d`, then cd into it `cd /tmp/tmp.QA8mBfFz1D`.
    -   Create a bash script that copies the password from bandit24 into a file that we created in the tmp folder.
        ```bash
        !#/bin/bash
        cat /etc/bandit_pass/bandit24 >> /tmp/tmp.QA8mBfFz1D/bandit24_pw
        ```
    -   This should read the password from the file and print it to the file specified in our script.
    -   gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
-   Level 24 -&gt; 25
    -   A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
    -   Let's see what is happening on port 30002:
        ```bash
        bandit24@bandit:~$ nc localhost 30002
        I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
        gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0000
        Wrong! Please enter the correct current password and pincode. Try again.
        gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0001
        Wrong! Please enter the correct current password and pincode. Try again.
        gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0002
        Wrong! Please enter the correct current password and pincode. Try again.
        ```
    -   Okay, so netcat shows us that there is a daemon asking for the current password and a numerical pin and gives back a reply. We can repeat this process numerous times without closing the connection. So the following steps should be done to find the pin via bruteforce:
        -   Create a temporary folder `mktemp -d`. Cd into the folder created
        -   Create a script file and make it executable
            ```bash
            bandit24@bandit:/tmp/tmp.5XoBXIIkjm$ touch brute_force
            bandit24@bandit:/tmp/tmp.5XoBXIIkjm$ chmod +x brute_force
            ```
        -   Create a list of all 9999 combinations of password and pin
            ```bash
            #!/bin/bash

            password=gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
            for i in ${seq -w 0 9999}; do
                echo "$password $i" >> combinations
            done
            ```
        -   Feed this list to netcat
            ```bash
            cat ./combinations | nc localhost 30002
            ..
            ..
            Wrong! Please enter the correct current password and pincode. Try again.
            Correct!
            The password of user bandit25 is /redacted/
            ```
    -   iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
-   Level 25 -&gt; 26
    -   Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.
    -   The user shell can be found in `/etc/passwd`; so we can find out the user shell via `cat /etc/passwd | grep bandit26`. It shows that it is something called `/usr/bin/showtext`.
    -   The content of this file is as follows:
        ```bash
        bandit25@bandit:~$ cat /usr/bin/showtext
        #!/bin/sh

        export TERM=linux

        exec more ~/text.txt
        exit 0
        ```
        So the file executes `more` with text file. A quick look at the manual for `more` gives us this:
        ```nil
        MORE(1)                                   User Commands                                  MORE(1)

        NAME
               more - display the contents of a file in a terminal

        SYNOPSIS
               more [options] file ...

        DESCRIPTION
               more is a filter for paging through text one screenful at a time. This version is
               especially primitive. Users should realize that less(1) provides more(1) emulation plus
               extensive enhancements.
        ```
    -   Next check the `home` folder of user bandit25: it handily contains a ssh key for bandit26. However, when we use this key to ssh into the server as user bandit26, the shell gets dropped immediately. It seems the text.txt file is too short to get us into `more` interactively - what can be done? Simple: just make the terminal window that you use for ssh smaller so that `more` enters interactive mode! We can then hit `v` to move into `vim` and gain a shell or read the password:
        -   Read the password: `:e /etc/bandit_pass/bandit26`
        -   Gain a shell: We can set a different shell for vim `:set shell=/bin/bash` and then use `:shell` for executing a bandit26 shell.
    -   s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
-   Level 26 -&gt; 27
    -   Good job getting a shell! Now hurry and grab the password for bandit27!
    -   When in the home folder of user bandit26, we see a file called `/bandit27-do`. We already know this type of file, it serves to run a command as another user, so a simple `./bandit27-do cat /etc/bandit_pass/bandit27` will give us the next password.
    -   upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
-   Level 27 -&gt; 28
    -   There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo via the port 2220. The password for the user bandit27-git is the same as for the user bandit27.
    -   Okay, this is about cloning a git repo locally. First, create a temporary folder via `mktemp -d` and `cd` into it. Then use `git clone ssh://bandit27-git@localhost:2220/home/bandit27.git/repo` and cd into the downloaded repo. There is a file called `README` which contains the password.
    -   Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
-   Level 28 -&gt; 29
    -   There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo via the port 2220. The password for the user bandit28-git is the same as for the user bandit28.
    -   Okay, this seems like the same exercise as the previous level. However, when we clone the repo as before, we see that the `README.md` file only contains the user and the password is redacted
        ```bash
        # Bandit Notes
        Some notes for level29 of bandit.
        ## credentials
        ​- username: bandit29
        ​- password: xxxxxxxxxx
        ```
        However, this is a git repo, remember?
    -   So let's snoop around a bit: `git log --oneline --graph --decorate --all`
        ```nil
        ​* 817e303 (HEAD -> master, origin/master, origin/HEAD) fix info leak
        ​* 3621de8 add missing data
        ​*
        ​* 0622b73 initial commit of README.md
        ```
        Ah, that is interesting, we see that there was an info leak which has been fixed in the latest release. So let's checkout a prior release: `git checkout 3621de8`.
    -   Now if we check `README.md`, we find the password.
    -   4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
-   Level 29 -&gt; 30
    -   There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo via the port 2220. The password for the user bandit29-git is the same as for the user bandit29.
    -   Proceed as in the previous levels:
        ```bash
        bandit29@bandit:/tmp/tmp.tVMV0827cM/repo$ cat README.md
        # Bandit Notes
        Some notes for bandit30 of bandit.
        ## credentials
        ​- username: bandit30
        ​- password: <no passwords in production!>
        ```
        So it says no passwords in production. Looking at the graph for `git log --oneline --graph --decorate --all` we see that there is a development branch:
        ```bash
        bandit29@bandit:/tmp/tmp.tVMV0827cM/repo$ git log --oneline --graph --decorate --all
        ​* 081ac38 (origin/dev) add data needed for development
        ​* 03aa12c add gif2ascii
        | * 7226732 (origin/sploits-dev) add some silly exploit, just for shit and giggles
        |/
        ​* 6ac7796 (HEAD -> master, origin/master, origin/HEAD) fix username
        ​* e65a928 initial commit of README.md
        ```
    -   Checkout one of the development branches: `git checkout 081ac38` et voila, the `README.md` file contains a password.
    -   qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
-   Level 30 -&gt; 31
    -   Start the same way as in the exercises before by making a tmp directory and cd'ing into it. Then clone the repo.
    -   Checking the file, we only see an empty file. The git history does not show any other commits, so no luck there. So we need to look somewhere else.
    -   In comes `git tag`. This usually serves to tag important releases (v1.0/2.0) etc. When we run `git tag` here, we see
        ```bash
        bandit30@bandit:/tmp/tmp.oN0GdVHuQ6/repo$ git tag
        secret
        bandit30@bandit:/tmp/tmp.oN0GdVHuQ6/repo$ git show secret
        ```
        Ah! We see that there is a tag called 'secret' - could be interesting! So let's look at it by using `git show secret` and find our password!
    -   fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
-   Level 31 -&gt; 32
    -   There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo via the port 2220. The password for the user bandit31-git is the same as for the user bandit31.
    -   Looking at the `README.md`, we see that we need to push a file to the remote repo to find out the password.
        ```bash
        bandit31@bandit:/tmp/tmp.cLzwKk9FvU/repo$ cat README.md
        This time your task is to push a file to the remote repository.

        Details:
        File name: key.txt
        Content: 'May I come in?'
        Branch: master
        ```
    -   So, first we need to create the file `touch key.txt`, then use a text editor or `echo 'May I come in?' > key.txt`.
    -   When we try to add the file to git, we see that it is ignored via .gitignore:
        ```bash
        bandit31@bandit:/tmp/tmp.cLzwKk9FvU/repo$ git add key.txt
        The following paths are ignored by one of your .gitignore files:
        key.txt
        hint: Use -f if you really want to add them.
        hint: Turn this message off by running
        hint: "git config advice.addIgnoredFile false"
        ```
        Luckily, git itself already gives us the necessary tools to add the file anyway using the `-f` flag.
    -   We then `git commit -m 'Bandit Level 31'` and push the repo to the remote:
        ```bash
        bandit31@bandit:/tmp/tmp.cLzwKk9FvU/repo$ git commit -m 'Bandit Level 31'
        [master 665c836] Bandit Level 31
        1 file changed, 1 insertion(+)
        create mode 100644 key.txt


        bandit31@bandit:/tmp/tmp.cLzwKk9FvU/repo$ git push -u origin master
        The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
        ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
        This key is not known by any other names.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        Could not create directory '/home/bandit31/.ssh' (Permission denied).
        Failed to add the host to the list of known hosts (/home/bandit31/.ssh/known_hosts).
                                 _                     _ _ _
                                    bandit31-git@localhost's password:
        Enumerating objects: 4, done.
        Counting objects: 100% (4/4), done.
        Delta compression using up to 2 threads
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 326 bytes | 326.00 KiB/s, done.
        Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
        remote: ### Attempting to validate files... ####
        remote:
        remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
        remote:
        remote: Well done! Here is the password for the next level:
        remote: /redacted/
        remote:
        remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
        remote:
        To ssh://localhost:2220/home/bandit31-git/repo
        ```
    -   3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
-   Level 32 -&gt; 33
    -   After all this git stuff, it’s time for another escape. Good luck!
    -   We get dropped into a "UPPERCASE" shell. And true enough: once we drop to the shell, we see that everything we type in gets translated to uppercases and we have no permission to run those. So, we need to think about things that are already uppercase in linux. And these are VARIABLES like `$SHELL`, `$HOME`, `$0` etc.
    -   Trying some of these variables we do at least see that we get the results back for them.
        ```bash
        Welcome TO THE UPPERCASE SHELL
        >> SHELL
        sh: 1: SHELL: Permission denied
        >> $SHELL
        WELCOME TO THE UPPERCASE SHELL
        >> $TERM
        sh: 1: xterm-256color: Permission denied
        >> $HOME
        sh: 1: /home/bandit32: Permission denied
        >> $PWD
        sh: 1: /home/bandit32: Permission denied
        ```
        However, we still are not allowed to run any of these variables/results (and running them would make no sense in most cases anyhow), so we need to think of sth. else. In comes `$0` which is the path of the current running program. And voila: if we run $0, we get dropped out of the shell.
        ```bash
        >> $0
        $ $0
        $ ls -la
        total 36
        drwxr-xr-x  2 root     root      4096 Sep 19 07:08 .
        drwxr-xr-x 70 root     root      4096 Sep 19 07:09 ..
        -rw-r--r--  1 root     root       220 Mar 31  2024 .bash_logout
        -rw-r--r--  1 root     root      3771 Mar 31  2024 .bashrc
        -rw-r--r--  1 root     root       807 Mar 31  2024 .profile
        -rwsr-x---  1 bandit33 bandit32 15136 Sep 19 07:08 uppershell
        ```
        A quick `ls -la` shows us the files, which are not very promising right now. But wait: we see that the `uppershell` script is actually run by the user bandit33. So maybe we are already bandit33?
        ```bash
        $ whoami
        bandit33
        $ cat /etc/bandit_pass/bandit33
        ```
    -   tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
-   Level 33
    -   And it seems we are done - for now:
        ```bash
        Congratulations on solving the last level of this game!

        At this moment, there are no more levels to play in this game. However, we are constantly working
        on new levels and will most likely expand this game with more levels soon.
        Keep an eye out for an announcement on our usual communication channels!
        In the meantime, you could play some of our other wargames.

        If you have an idea for an awesome new level, please let us know!
        ```
