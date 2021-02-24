# cron

**Last update**: 20210224



### To do:

1. Read Wikipedia entry
2. Read entries from the book
3. Beautify the real-life examples



**cron** : handles recurring jobs.  


Cron is most suitable for scheduling repetitive tasks. Scheduling one-time tasks can be accomplished using the associated **at** utility.



=> the daemon which does the job is **cron(d)**  => it checks for new jobs every minute



o handling regularly recurring tasks

o runs in the background and regularly runs scheduled jobs

o it is persistent, also after rebooting! 

o individual tasks: **cronjobs**

o tasks management: **crontabs**

o **crontab** is a table with 6 columns that defines when a specific job is to be performed

  => each command in the crontab occupies a single line

  => fields 1-5 specify time, field 6 specify command to be run (including any parameters)

o making a **crontab** table:

```bash
crontab -e # -e indicated editing
```

o making a **crontab** table as root:  

```bash
crontab -u someUser -e # making a crontabe for some user
```

o by default, in my case 'nano' is opened to edit crontab. Alternatively, you can select a custom editor via

```bash
export EDITOR=/usr/bin/gedit
crontab -e
```

o crontab lines are not allowed to contain line breaks

o allowed content of fields:

```bash
1: minutes (0-59) and * wildcard
2: hour (0-23) and * wildcard
3: day (1-31) and * wildcard
4: month (1-12, Jan to Dec, jan to dec) and * wildcard
5: weekday (0-7, where both 0 and 7 mean Sunday, Sun to Sat, sun to sat) and * wildcard
6: someComand + its options
```

o the values in the field can be separated by commas or by ranges. For instance, in the 5th column we can have:

```bash
0,3 or Sun,Wed 
1-5 or Mon-Fri
```

o a slash followed by a number indicates regular periods of time. For instance, in the 2nd column we can have:

```bash
*/2 => every two hours
1-6/2 => 1,3,5
```

o the crontabs are typically stored in ```/var/spool/cron/crontabs/<user-name>```



**Cron permissions (from Wikipedia - refurbish a bit)** 

These two files play an important role:

- **/etc/cron.allow** - If this file exists, it must contain the user's name for that user to be allowed to use cron jobs.
- **/etc/cron.deny** - If the cron.allow file does not exist but the /etc/cron.deny file does exist then, to use cron jobs, users must not be listed in the /etc/cron.deny file.



Example entries in the crontabs:

```bash
1/ Run some command at 07:00 each morning
0 7 * * * source someScript.sh
```

o list your own entry in crontab

```crontab -l```

o to delete entries, just edit the crontab directly with crontab -e

o global crontab entries are here: ```/etc/crontab```

=> this one has extra field for 'user'



o From the internet: how to check if cron is running at all?

```systemctl status cron```

=> these are log files of cron



o From the internet: How to tell cron to print on the terminal?

```bash
$tty # getting the pts of terminal you want to print
/dev/pts/7
# then in crontab add an entry
* * * * * date | tee /dev/pts/5 /dev/pts/6
```

\# this will print simultaneously to two terminals corresponding to /dev/pts/5 and /dev/pts/6



