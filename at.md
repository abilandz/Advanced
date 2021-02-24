# at

**Last update**: 20210224



### To do:

1. Read Wikipedia entry
2. Read entries from the book
3. Beautify the real-life examples



**at** : handles one-time tasks  



=> the daemon which does the job is **atd** => it checks for new jobs every minute



**at** 

o it is persistent, i.e. it will keep running after the machine is rebooted

scheduling a job

Concrete self-evident example:

```bash
$ at 16:16 # type this in the terminal, then the prompt appears where I can type in the jobs
at> rm someFile # just some trivial example
at> # hit Ctrl+D to close the 'at' scheduling
```

With the above syntax ```someFile`` will be deleted automatically at 16:16. 

o after completing a task, **at** sends an email to the job owner. This can be enforced with -m flag

o displaying scheduled jobs: **at -l** or **atq**

```bash
$ atq
1      Tue Feb 16 12:31:00 2021 a abilandz
2      Tue Feb 16 12:44:00 2021 a abilandz
```

The output is: job number, time of execution, queue name and the owner

!!! IMPORTANT: I cannot see this way what is the scheduled job !!!

But if I have root privileges, I can inspect the content of files in 

```sudo cat /var/spool/cron/atjobs/SomeFile```

=> each file here contains the job description

o shortcuts for scheduling: see Table 1 on p83

o deleting scheduled jobs: **at -d** or **atrm**

```bash
$ atrm 1
$ atq
2      Tue Feb 16 12:44:00 2021 a abilandz
```

o access to use **at** is controlled via two files:

```bash
/etc/at.deny
/etc/at.allow
```

By default, only 'at.deny' is present. If you manually provide also 'at.allow', then 'at.deny' is not parsed.

```bash
$ sudo cat /etc/at.deny
alias
backup
bin
daemon
ftp
games
gnats
guest
irc
lp
mail
man
nobody
operator
proxy
qmaild
qmaill
qmailp
qmailq
qmailr
qmails
sync
sys
www-data
```



Example: How to force **at** to print something on a given terminal?

```bash
# open a terminal and execute tty in it
$tty
/dev/pts/6
# now run at, and in its prompt use that info
at 10:05
warning: commands will be executed using /bin/sh
at> echo Hallo! > /dev/pts/6
at> <EOT>
job 4 at Wed Feb 24 10:05:00 2021

```



Example: How to send some reminders to all active terminals with **at**, at specified time?

```bash
$ at 10:18
warning: commands will be executed using /bin/sh
at> echo Reminder | tee /dev/pts/*       
at> <EOT>
job 5 at Wed Feb 24 10:18:00 2021
```

