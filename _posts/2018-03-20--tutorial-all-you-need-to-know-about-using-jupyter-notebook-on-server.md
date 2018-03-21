---
layout: post
published: true
categories:
  - personal
mathjax: false
featured: false
comments: false
title: '[Tutorial] All You Need to Know about Using Jupyter Notebook on Server'
---
In this post I will talk about three things on how to set up Jupyter Notebook on server:

# 1. How to run Jupyter on server and edit notebooks on web browser
Long story short, a server is unlike our local machine -- when you run "jupyter notebook", no browser will be opened for you. What we need to do is 

## (1) on server, run "jupyter nobebook --no-browser --port=8888" (8888 is arbitrary)
This is preferably in a screen/tmux session (e.g., "screen -S screen_name"), so that next time won't need this step.
If you are as unfortunate as I am and run into a distributed system: 
### • in the screen session enter "unsetenv KRB5CCNAME"
### • "kinit", 
### • enter your password, 
### • "ctrl-a then d" and exit the server and log back in and "screen -r" back to your screen
### • enter "aklog"
## (2) in your local machine terminal, enter "ssh -N -f -L localhost:8888:localhost:8888 username@server.name" (this is called "tunneling", but who cares what it is called).
## (3) copy the website address in (1), paste it to your browser, and hit "enter".

Note that once this is set up, jupyter will keep running

# 2. What if when I am running step (2), the port is already in use?
Enter "lsof -ti:8888 | xargs kill -9" to free the port from being occupied, and then run step (2).

# 3. How to do everything in 1 and 2 just once
The above chores are tiring. Let's save ourselves some time and automate this whole process.

## (1) in your ~/.cshrc (or maybe in your case, ~/.bashrc) on server, add a line anywhere like this "alias notebook jupyter notebook --no-browser --port=\!:1", so that next time you can just type "notebook 8888" to run jupyter.
## (2) in your local machine terminal, create a shell script named "free_and_create_port.sh" (or some other name) with these two lines "lsof -ti:$1 | xargs kill -9" and "sshpass -p yourpassword ssh -N -f -L localhost:$1:localhost:$1 username@server.name". Then each time you want to connect to the server, simply run "sh free_and_create_port.sh 8888", you should be reconnected to the notebook. If you forget the address, go back to the screen session on server and hit "ctrl-c", resume, and find out.

# 4. What if the server I run Jupyter is multiple hops away?
First of all, my condolence. Suppose you need to go local-->server A--> server B, then simply type "ssh -N -f -L localhost:8888:localhost:8888 username@serverB.name" on server A, and on local machine enter "ssh -N -f -L localhost:8888:localhost:8888 username@serverA.name".

After all this madness, you will be done. Done and done.
Good luck!
