# rsync

**Last update**: 20211222



Very frequently we develop and use the same code on multiple computers - how to ensure best that the changes on one computer are automatically propagated onto another computers where our code sits? This can be accomplished with **git**, but for the simpler cases we can also use **rsync**. The latter can be learned within 5 minutes, while for **git** learning curve is much longer (but proportionally the gain is bigger). 

**rsync** - perfect synchronization tool

o it uses SSH to encrypt the data

o the target computer needs to have copy of a previous version

o it checks for differences between source and target - checksum search algorithm (developed by Samba team)

o **rsync** only transfers the differences between the two sides

```bash
rsync [options] source target
# source - here we have done some changes
# target - here we want to propagate those changes
```

or

```bash
rsync [options] source remoteComputer:target
```



etc

o target can be on a local or on remote machine

-n : dry run, whenever I am not sure what is source and what is target

-v : verbose

Example: To mirror a directory dir1 on a local machine

```bash
rsync dir1/* dir2/
# this tranfer only normal files, but it skips subdirs, symlinks, etc
# -r : sync recursively all subdirs
# -l : sync also symlinks
# -L : sync also symlinks, but now they are in <target> normal files
```

**IMPORTANT: It is essential to use dir2/ instead of dir2 in the example above**

```bash
rsync -a source/folder target
# this transfers folder, if it doesn't exist, rsync will create it
rsync -a source/folder/ target
# this transfers only the content if folder exists
# In the two cases above, the difference is weather the file source/folder/foo.txt is
#  transferred into source/folder/foo.txt or source/foo.txt
```

o if rsync is used for backuping, it makes sense of keep all file metadata:

-a : retains all file attributes

o rsync can omit files from sync:

-- exclude=somePattern

rsync -a --exclude="*.pdf" sourceDir/ targetDir/

for multiple exclusions, do:

1/ specify the file, e.g. myExclusions.txt:

"*.pdf"

"*.png"

2/ use then a slightly different flag:

```bash
rsync -a --exclude-from=myExclusions.txt sourceDir/ targetDir/
```

o delete data that is no longer needed, e.g. you have some files in the backup, but not any longer in the source 

 => this way you keep the original version of the copy in target, after you have deleted some file in the source 

--delete

--delete-after

--delete-excluded

IMPORTANT: If you mixed up source and target, --delete will happily delete some source file!!!!

--progress : gives you a nice progress info per each file

o the rest:

**-z** : compress data

**Grsync** : graphical front-end for rsync

**--partial** : if sync fail, you will continue from the point where you failed (use with care, though, see last comment in the box on p77)



Example: Your central work directory is on Computer A, on a hardisk A-HD1, in directory Dir1. Develop a script which will sync your work regularly with:

1/ hardisk HD2, in directory Dir1_backup.

2/ Computer B, on a hardisk B-HD1, in directory Dir1_backup, 

3/ Computer B, on a hardisk B-HD2, in the work dir Dir 1

Implement a call to the script via crontab and ~/.bash_logout

```bash
#!/bin/bash

# TBI: I implemented this script straight, but didn't test it

SourceDir=/home/abilandz/TEMP/Dir1
TargerDirs=( 
/home/abilandz/TEMP/Dir2  
ga45mof@transfer.ktas.ph.tum.de:/home/abilandz/TEMP/Dir1 
ga45mof@transfer.ktas.ph.tum.de:/home/abilandz/TEMP/Dir1_backup 
)

for TargetDir in ${TargetDirs[*]}; do 
 rsync -ar ${SourceDir}/* ${TargetDir}/
done

return 0
```







