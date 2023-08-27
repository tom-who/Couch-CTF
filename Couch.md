# Couch-CTF
Step by Step tutorial for the Couch CTF on tryhackme

### Link: https://tryhackme.com/room/couch

## Enumeration

Lets begin with nmap, a popular enumeration tool!

*more here: https://www.kali.org/tools/nmap/*

Command: `nmap <machine_ip> -sV -sC -p-` (*We use -sV to determine the service version of the services running on the open ports and we use -p- to scan all ports, because by default nmap only scans 1000 ports, we can change it to scan all 65,000 using -p-*)

Anways! Lets look at our results

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/70566c9c-dae9-477c-b143-84b559cee369)

We see that port 22, and irregulary, port 5984 is open aswell, and we see that is running http, ontop of running a DBMS (Database management system) So lets access this webiste by going to the following url on a web browser

`http://<machine_ip>:5984`

We are greeted with an apache default page, this likely doesnt contain any useful information unless it is changed by the website developer! 
Using directory enumeration tools like dirb or gobuster wont work in this scenario, because all requests come back with an HTTP status code of 200 because of the way CouchDBMS is designed
So we look online for information on Couch, this took me to *https://docs.couchdb.org/en/stable/api/index.html* 

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/f78ca169-b0dd-4bc5-953b-bcaf44dda537)

We see if that we add /_utils we can get a GUI for the data on this webserver which I went ahead and did

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/6f570840-1f91-48e9-9b3c-49d745bbad6d)

We see there are a number of folders, the most important one just by the looks of it being "_users" and "secret"
To my surprise, "_users" didnt turn up much so I went to "secret" 

This was alot more bountiful as it showed me a username and password under the term "passwordbackup"

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/c2d01925-23b6-4169-9df1-ce6054973b8b)

So , recalling back to the nmap scan we remember there was an SSH port running, we now have credentials so its time to access the machine via SSH

### SSH Access and Privilege Escalation

On Login, we have the user flag, which you can claim, but now its time to try get the root flag, well by trying to change to /root unsuprisingly we are denied permission

Running `sudo -l` to find out what we might be able to run as sudo, there is nothing.

I resorted to manual enumeration, by running the command `ls -lah` in the directory of the user flag, we come across a ".bash_history" file

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/a2fa94e4-311a-457d-9dfb-779375e099c1)

Bottom of this file: 

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/72e6254b-5051-4143-9c5c-19bf4a303174)

We see a command that might be worth running, and thats exactly what we do, after executing this command and waiting about 5 seconds, we are given a root shell, we can check this by running `whoami` or `uid`

![image](https://github.com/traveller404/Grep-CTF/assets/92340426/57fa6fe2-be2c-4766-a22a-b128cb174a8d)

Now all we have to do is find the location of the root flag, its not in the default /root directory but changing to /dev/root reveals root.txt

Hope this helps!




