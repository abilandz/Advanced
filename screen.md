# screen

**Last update**: 20201206



### To do:

1. Read Wikipedia entry
2. Concrete real-life examples



Since there was today a question on how to detach all processes from the terminal, I am now supplying my online reply with the brief tutorial on 'screen', which typically I am sending to the students under my supervision, and which won't be covered during this lecture in more detail.

This is the summary of basic 'screen' commands, which is really a great software if you want to do your thing without interruption from multiple machines. For instance, if you have a process running in 'screen' on your desktop machine in the office, then you can detach from that screen session, go somewhere else, and re-attach to that screen session remotely from any other machine in this world, and continue just like you are sitting in front of your desktop machine.

  When in terminal:
  \- screen -S <name> # start a new screen with <name>
  \- screen -ls # what are the running screen sessions on my machine?
  \- screen -rd <screen-session-name> # re-attach to particular screen, from anywhere in the world, e.g. screen -rd 3612.pts-12.anteb
  \- kill -9 <screen-PID> # where <screen-PID> you can get from screen -ls, e.g. in "3612.pts-12.anteb" screen-PID is 3612
  \- screen -wipe <screen-name> # after you killed the certain screen, use also this command to wipe it out from history

  When in 'screen':
  \- Ctrl+a+d # detach from screen
  \- Ctrl+a+c # make new session in the screen
  \- Ctrl+a Shift+a # name a new session in the screen
  \- Ctrl+a Shift+" # menu of screen sessions
  \- Ctrl+a+K # kill the current session in the screen