# GoldenEyeCTF
TryHackMe writeup for the GoldenEye CTF room

In this James Bond-themed guided CTF, we will walkthrough the process of gaining access into the machine and escalating our privileges to become root. Let us begin!

First things first, as we don't have any prior information given to us about this machine beside the machine's IP, we can start off by conducting an Nmap scan to discover any open ports that may be of use to us:

![image](https://user-images.githubusercontent.com/53369798/111050929-84b05a00-841d-11eb-9629-0557b70a4b46.png)

There are four open ports: 25, 80, 55006, and 55007

The easiest to check of these services would be the web server (80). Here's what happens when we browse to the destination:

![image](https://user-images.githubusercontent.com/53369798/111050979-d48f2100-841d-11eb-83a9-9ca6a06b36f1.png)

Hmm, before we move onto the "/sev-home/", let's check out the source code to the page just to see if there is anything interesting:

![image](https://user-images.githubusercontent.com/53369798/111051273-e4a80000-841f-11eb-8b4e-949570b23792.png)

There doesn't seem to be much, but we can quickly look at "terminal.js" to see if there is anything of interest:

![image](https://user-images.githubusercontent.com/53369798/111051265-d823a780-841f-11eb-9fbb-9f3e4a981d95.png)

We found a note to one of the potential users, "Boris", along with his supposed encoded password. To help decode this password easily, I've used CyberChef with the "Magic" recipe in order to reveal the original plaintext:

![image](https://user-images.githubusercontent.com/53369798/111051454-25ecdf80-8421-11eb-951e-61756bc19eaa.png)

Now that we have some credentials to use, let's put them to good use:

![image](https://user-images.githubusercontent.com/53369798/111052066-c47b3f80-8425-11eb-8257-bae3b13c00dd.png)

Cool information, but what really caught my attention was the large amount of whitespace underneath the initial homepage code. Here's what has been revealed:

![image](https://user-images.githubusercontent.com/53369798/111052119-48cdc280-8426-11eb-9c48-a1bb696eb9fc.png)

We confirmed that two users are available to for usage: Boris and Natalya. Utlizing some wordlists, let's see if we can discover their credentials:

![image](https://user-images.githubusercontent.com/53369798/111052139-8894aa00-8426-11eb-84df-0612cbcbeaa3.png)

![image](https://user-images.githubusercontent.com/53369798/111052283-9565cd80-8427-11eb-99de-62cff69c59ca.png)

With some credentials to use for the POP3 service, we can now try logging in. I'll start with Boris first:

NOTE: Prior to this room, I didn't know that you could use Telnet to log into a POP3 service!

![image](https://user-images.githubusercontent.com/53369798/111052716-6cdfd280-842b-11eb-9367-c5a6e950f10c.png)

AS you can see, Boris has three emails. After inspecting all three, the third one in particular holds some considerable value to us:

![image](https://user-images.githubusercontent.com/53369798/111052765-ed063800-842b-11eb-9ee5-04a9fd6a51e1.png)

We learn that Boris's boss is possibly named Alec ("alec@janus.boss"), the final access codes are in a hidden file within the root directory, as well as someone (or something) named Xenia. Let's dig a little deeper and look through Natalya's emails:

NOTE: Had to start up a new machine, I forgot to keep the old machine up and running before time expired. :)

![image](https://user-images.githubusercontent.com/53369798/111052957-dfea4880-842d-11eb-9914-4906d34c5429.png)

Natalya has two emails, here's the first:

![image](https://user-images.githubusercontent.com/53369798/111053457-f72b3500-8431-11eb-9f17-991c3e2fc1d2.png)

And now here's the second:

![image](https://user-images.githubusercontent.com/53369798/111053464-0c07c880-8432-11eb-9fbb-31bd618172d5.png)

The second email contains the login credentials for Xenia! We also have instructions as to entering their internal domain (severnaya-station.com).

With this information, we can add this new domain into the hosts file (/etc/hosts/) in our attacking machine:

![image](https://user-images.githubusercontent.com/53369798/111053517-6f91f600-8432-11eb-8fa9-5b0b42e319ba.png)

And here is the website when we first load in:

![image](https://user-images.githubusercontent.com/53369798/111053527-889aa700-8432-11eb-917e-0a1b7d70869c.png)

We should navigate to the login page and use Xenia's account to see what else we can find:

![image](https://user-images.githubusercontent.com/53369798/111053575-e3340300-8432-11eb-988f-dea699a22aa6.png)

