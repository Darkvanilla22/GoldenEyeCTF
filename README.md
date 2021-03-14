# GoldenEyeCTF
TryHackMe writeup for the GoldenEye CTF room

In this James Bond-themed guided CTF, we will walkthrough the process of gaining access into the machine and escalating our privileges to become root. Let us begin!

First things first, as we don't have any prior information given to us about this machine beside the machine's IP, we can start off by conducting an Nmap scan to discover any open ports that may be of use to us:

![image](https://user-images.githubusercontent.com/53369798/111056399-b260c780-844c-11eb-876a-715ab244a84e.png)

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

The login was successful!

When logging into a new account, I'd like to see what sort of information is now accessible to me by using said account. Here's the profile page to Xenia's account:

![image](https://user-images.githubusercontent.com/53369798/111053723-38244900-8434-11eb-9531-66598a9bdad9.png)

After snooping around for a bit, I have come across the only message that Xenia seems to have in their inbox:

![image](https://user-images.githubusercontent.com/53369798/111053755-918c7800-8434-11eb-9c2f-c3d024df31b5.png)

This a conversation with Dr. Doak, a Training Scientist. As this is another user we've found, let's see if we can gain access into their POP3 account by initiaing a dictionary attack through Hydra:

![image](https://user-images.githubusercontent.com/53369798/111053828-1d060900-8435-11eb-9433-b6fa6c704195.png)

Yea, seems like nobody here has any regarding for stronger passwords. Let's get into Doak's emails:

![image](https://user-images.githubusercontent.com/53369798/111053991-c4377000-8436-11eb-98ff-4526f42351dd.png)

More login credentials for us to abuse! Back to the website, we can login as "dr_doak", continuing our search for more information.

After a little more snooping around, I've managed to stumble across this file in the "Private Files" section:

![image](https://user-images.githubusercontent.com/53369798/111054164-33fa2a80-8438-11eb-956f-aaee6ec81ab2.png)

I downloaded the file onto my attacking machine and inspecting the contents of this file:

![image](https://user-images.githubusercontent.com/53369798/111054171-55f3ad00-8438-11eb-8a33-b0bd38b1b84e.png)

And now off to the designated location of this picture of interest:

![image](https://user-images.githubusercontent.com/53369798/111054218-e7631f00-8438-11eb-9aa9-1b28818b044f.png)

At face-value, there doesn't seem to be anything spectacular about this picture at all. However, we can use Exiftool to view the metadata for this picture:

![image](https://user-images.githubusercontent.com/53369798/111054313-99025000-8439-11eb-83ed-5e5cc2119163.png)

Right off the bat, I've noticed that the image description has encoded string in Base64. Putting it into CyberChef with the "Magic" recipe set reveals the password to the admin account:

![image](https://user-images.githubusercontent.com/53369798/111054348-eaaada80-8439-11eb-9fcd-3c2b0865d555.png)

As the website's admin account has kept the "admin" username, we can pair that with the discovered password in order to login to the website with heighten privileges, such as being able to edit the website!:

![image](https://user-images.githubusercontent.com/53369798/111054420-98b68480-843a-11eb-8378-32f42cddfaf5.png)

Ok, so now that we've gained access as an admin, we need to figure out a way to leverage ourselves into the operating machine through the website. A good way of accomplishing this would be to establish a connection via reverse shell. We can do this by exploiting a remote code injection into the Aspell plugin path found on the website. I will be using a piece of code from Swisskyrepo's PayloadAllTheThings (https://github.com/swisskyrepo/PayloadsAllTheThings/):

NOTE: I needed a little bit of a hint to find out where this plugin may be at, so I google search "moodle aspell" to find the official documentation on how to use Aspell!

![image](https://user-images.githubusercontent.com/53369798/111054921-79b9f180-843e-11eb-8c30-6d0169d085fc.png)

Ok, now to conduct this exploit, we need to create a page in which we can use Aspell on. I've just added a new course and named it "HACKED". The entire point of this is act as a way to "spellcheck" something. In other words, execute our code.

NOTE: I also had to do an extra step to make the spellcheck actually "work". I needed to change the spellcheck engine from the Google Spell to PSpell Shell.

![image](https://user-images.githubusercontent.com/53369798/111055264-382b4580-8442-11eb-8f65-81e294ba07f5.png)

Alright, with everything checked and ready to go, let us commence with the exploit. If it works, then we get a connection to our Netcat listener we had set up:

![image](https://user-images.githubusercontent.com/53369798/111055297-7de80e00-8442-11eb-8aa5-652c6b8bf442.png)

It worked!

From here, let's do some enumeration within the machine. We've got a pretty convient script for this (LinuxPrivChecker), so we need to get it onto this machine. To do this, we can spawn an HTTP server on our attacking machine through a pre-installed Python module called "SimpleHTTPServer". From there, we can run a "wget" command to grab a copy of that script and place it on the victim machine:

![image](https://user-images.githubusercontent.com/53369798/111055582-4890ef80-8445-11eb-8e6d-e68cedda1cb6.png)

After running the script, I was able to find the developments tool (CC) needed in order to perform the privilege escalation:

![image](https://user-images.githubusercontent.com/53369798/111055984-50529300-8449-11eb-884f-2c771ab5e2ec.png)

Knowing this now, I can utilize CVE 2015-1328 (Overlayfs local root exploit) found here: (https://www.exploit-db.com/exploits/37292).

In order for this exploit to work properly, we should navigate to the "/tmp" directory and place our uncompiled C file there. Also, we need to examine the code itself to make sure it will work under the right circumstances within the target enviornment. This means we need to switch "gcc" to "cc":

![image](https://user-images.githubusercontent.com/53369798/111056104-7b89b200-844a-11eb-820d-359b05854975.png)

And now to compile the code, execute the binary, and become root!!!

![image](https://user-images.githubusercontent.com/53369798/111056164-fb178100-844a-11eb-9fca-e997b3cad8e9.png)

Now that we're root, let's go and find that flag:

![image](https://user-images.githubusercontent.com/53369798/111056232-7711c900-844b-11eb-8973-02d71af9e763.png)

Flag has been captured, and it looks like there is one more thing I should check out:

![image](https://user-images.githubusercontent.com/53369798/111056263-9dcfff80-844b-11eb-82ab-253527a83aef.png)

And that's the end! Overall, I really enjoyed this room. I actually learned a good amount of stuff from this, as well as appreciating how long this CTF was. A great medium-level room indeed!
