qjob
====

A distributed job/process queuing client and daemon using beanstalkd

A simple example:

$ qjobd --exec HandBrakeCLI --tube encode --host localhost
$ qjob --tube encode -- --preset "High Profile" -m -4 -O -t 1 -i "~/Video/TV Show Season 2.iso" -o "~/Video/TV Show Season 2 Title 1.mp4"
$ qjob --tube encode -- --preset "High Profile" -m -4 -O -t 2 -i "~/Video/TV Show Season 2.iso" -o "~/Video/TV Show Season 2 Title 2.mp4"
$ qjob --tube encode -- --preset "High Profile" -m -4 -O -t 3 -i "~/Video/TV Show Season 2.iso" -o "~/Video/TV Show Season 2 Title 3.mp4"
$ qjob --tube encode -- --preset "High Profile" -m -4 -O -t 4 -i "~/Video/TV Show Season 2.iso" -o "~/Video/TV Show Season 2 Title 4.mp4"

In this example, we start the qjobd daemon that listens to the beanstalkd server on tube 'encode'.  We then start feeding it work with 
the qjob commands to convert four separate episodes from a DVD ISO.  

On the qjob command, anything after the '--' is sent to the 'encode' tube which is read by the qjobd daemon.  The 
qjobd daemon appends the contents of the tube entry as command line arguments and runs the executable (e.g. HandBrakeCLI).

This simple example isn't very impressive as each video is converted serially.  This could be accomplished 
using a bash script.
The value of this comes in when your data/files are sitting on a file server (likely NFS) and 
multiple computers run the qjobd daemon.  With this type of setup, the videos 
can be converted concurrently with each computer pulling from the tube and executing work.


Setting up
==========
Prerequisites
-------------
If your not familiar with beanstalkd, please see https://github.com/kr/beanstalkd.  Its available in most *nux distro repositories so 
try installing using apt-get, yum, pkg, or whatever your preferred *nix flavor uses for installing a package.

Install the beanstalkd client queueit.  I've forked queueit to fix a minor issue.
$ git clone git://github.com/srlefevre/queueit.git queueit
$ cd queueit
$ sudo python setup.py install


Install qjob
------------
Get the code
$ git clone git://github.com/srlefevre/qjob.git qjob
$ cd qjob

Create the configuration and logging directory
$ mkdir ~/.qjob

Copy the example configuration files
$ cp qjob.conf qjobd.conf ~/.qjob/

Copy scripts/programs into the path
$ chmod a+x qjob qjobd
$ sudo cp qjob qjobd /usr/local/bin
You can put them anywhere that's in your path such as ~/bin/

Configuration
-------------
The ~/.qjob/qjob holds the defaults for the qjob client.
The ~/.qjob/qjobd holds the defaults for the qjobd daemon.
Just edit using your text editor of choice (nano, joe, vi, gedit, etc)

All defaults can be overridden by command line arguments of their respective commands.

Use qjobd
===
Start qjobd daemon
$ qjobd --exec <exec name> --tube <tube name>

End qjobd daemon
$ qjobd --tube <tube name> --kill

Platforms
=========
These scripts have been tested on CentOS 6.5, Ubuntu 12.04, and OpenIndiana 151.a8.  I expect that they will
work on any *nix platform that supports python and has bash installed.

WARNING and LEGAL DISCLAIMER OF LIABILITY
=======
The qjobd daemon and qjob client do not authentication or authorize anything that is feeding the job queue
tube(s).  This software is intended to be used in an enclosed and secure computing environment as someone 
could use it maliciously.  

If you set the software up, you are responsible for how it is used.  The author takes no responsibility and disclaims 
all liability for any damages, issues, problems, headaches, catastrophes, etc that may be incurred.  


