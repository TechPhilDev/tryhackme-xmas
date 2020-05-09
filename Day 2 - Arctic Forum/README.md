# Day 2 - Arctic Forum

> Note: From this day, I started documenting as i went.

> This should mean that documentation is more in-depth

Day 2's challenge involved brute-forcing directories on a remote server.

For this, we are instructed to use [Dirsearch](https://github.com/maurosoria/dirsearch), a python script which can essentially ping different directories and tell us which ones exist.

It uses a wordlist to know what directories to ping.
We obtained ours as part of [this DirBuster download](http://downloads.sourceforge.net/dirbuster/DirBuster-Lists.tar.bz2?use_mirror=osdn)

 <sup><sub>Wow this VPN is slow</sub></sup>

We will be using `directory-list-2.3-medium.txt` as advised by the documentation for this day.

Using command
`python3 ./dirsearch.py -u http://10.10.151.166:3000 -w ../DirBuster-Lists/directory-list-2.3-medium.txt`, the program starts running.

 <sup><sub>Wow it seems this wordlist is even longer!</sub></sup>

 I'm running the script in VS.Code's built in terminal, and I think that may be posing some issues with rendering occasionally.

 In order to combat this, I had to press <kbd>Ctrl</kbd> + <kbd>C</kbd> occasionally. Luckily dirsearch handles this and gives you an opportunity to continue - This is good, as we don't lose our progress. Just have to enter <kbd>c</kbd> and press <kbd>ENTER</kbd> to carry on.

 <sup><sub>Just seen the wordlist size - 220,521!</sub></sup>

 We find the following directories - 
 ```
  _|. _ _  _  _  _ _|_    v0.3.9
(_||| _) (/_(_|| (_| )

Extensions: html | HTTP method: get | Threads: 10 | Wordlist size

Target: http://10.10.151.166:3000

[23:06:21] Starting: 
[23:06:21] 302 -   28B  - /  ->  /login
[23:06:22] 302 -   28B  - /home  ->  /login
[23:06:22] 200 -    2KB - /login 
[23:06:23] 302 -   27B  - /admin  ->  /home
[23:06:23] 302 -   28B  - /Home  ->  /login
[23:06:23] 301 -  179B  - /assets  ->  /assets/
[23:06:25] 301 -  173B  - /css  ->  /css/
[23:06:27] 200 -    2KB - /Login
[23:06:28] 301 -  171B  - /js  ->  /js/
[23:06:30] 302 -   28B  - /logout  ->  /login
[23:06:48] 200 -    2KB - /sysadmin
[23:07:02] 302 -   27B  - /Admin  ->  /home
[23:07:43] 302 -   28B  - /HOME  ->  /login
[23:08:01] 302 -   28B  - /Logout  ->  /login
[23:16:00] 200 -    2KB - /SysAdmin      
[23:17:52] 200 -    2KB - /LogIn
[23:23:17] 200 -    2KB - /LOGIN
Task Completed
```

302 and 301 codes are redirects, and the -> signifies what they redirect to.
200 codes are 'OK' - we get something.

`/sysadmin` looks interesting. Let's see what's there..

So, beleive it or not, but all this actually took so long that my machine expired.
> Remember that terminal output doesn't show all the directories that returned 404 errors!

 I'll just deploy a new one.

A slightly different IP this time, as was to be expected - `10.10.62.239`. This isn't a problem. We don't need to rescan as the directories will be identical.

Navigating to `/sysadmin` gives us an admin login page.

There's nothing immediately obvious to me, so let's look at the source code.

We see an html comment - 
```html
<!--
    Admin portal created by arctic digital design - check out our github repo
    -->
```
This looks promising. Let's search GitHub for 'arctic digital design'

We get one result back - a [GitHub Repo](https://github.com/ashu-savani/arctic-digital-design).

The repo's `README.md` is very basic - 
>arctic digital design used for advent of cyber
>
>Previous versions of this software have been shipped out. The credentials to log in are:
>
>username: admin
>password: defaultpass
>
>** the login portal accepts usernames instead of emails **

<sup><sub>I'm not entirely sure this is the original repo, as I am doing this nearly 6 months later</sub></sup>

Great! This tells us our default credentials - let's try these on the login form!

And we're in!
Here we see the answer to one of the other questions -
>Please don't forget to BYOE(Bring Your Own Eggnog) for the partay!!

We're done! Now let's terminate our VM so we don't use up resources.