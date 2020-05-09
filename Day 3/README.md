# Day 3 - Evil Elf

### Questions
- What's the destination IP on packet number 998?
- What item is on the Christmas List
- Crack Buddy's password

---

It's Day 3, and today we need to do some packet analysis.

I already have Wireshark installed, but it can be downloaded from the [official website](https://www.wireshark.org/download.html) (Note: I'm running Windows 10)

We will begin by downloading our network capture file. It's in the `.pcap` file format. We can open it in Wireshark, and we are presented with a long list of packets. And an update dialog. Ugh. BRB!

Ok, now that I've successfully clicked 'Remind me later', we can start on our first question - 

### What's the destinaton IP on packet number 998

This is easy. We can just scroll down to packet 998, where we can see our destination IP as `63.32.89.195`

### What item is on the Christmas List?

This one is a bit trickier. We need to find a mention of the christmas list.

Luckily the documentation for this day gives us a hint - TELNET.

By typing `telnet` into the WireShark filter box, we see 3 packets - `2255`, `2906` and `2908`

The first of these packets contains the following data -
`echo 'ps4' > christmas_list.txt`

Perfect! The answer is ps4!

Remember, always read the docs!

### Crack Buddy's password

Now, this is the difficult one, but as always the docs are our friend.

It tells us that the other two telnet packets are related to this problem - 

Packet `2906` shows a user sending `cat /etc/shadow` to a remote linux terminal - (`cat` just prints the contents of a file to the teminal)

`/etc/shadow` actually contains user data, in the format `username:$hash_algorithm$hash_salt$hash_data:other_data`

Our data in this case (at least for our 'buddy' user) is `buddy:$6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1:18233:0:99999:7:::`

We can separate this into the component parts as follows - 

- Username - `buddy`
- $hash_algorithm - `$6`
- $hash_salt - `$3GvJsNPG`
- $hash_data -`$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1`
- :other_data - `:18233:0:99999:7:::`

With this information, we can now make steps towards cracking this.

We will be using a tool called [Hashcat](https://hashcat.net/hashcat) for this.

We will also need a [wordlist](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt) to help us aswell.

Ah. At the time of writing, the website to download hashcat is down. I could manually build it on my Windows PC, but I think now would be the best time to fire up Kali!

Luckily, Kali already has hashcat installed, so it's just a case of downloading the wordlist (which we can do using `wget`) and running the command.

However, before we can do that, we need to do some research. As seen above, `$6` is our hash algorithm, but what does that mean? The docs point us to the hashcat website, but that's down... Luckily, they lay it our clear as day - 
>We can search for $6 and see the hash-mode for Hashcat is 1800 and the type is using sha512crypt.

Perfect! We know our mode for hashcat is `1800` - if you were using another tool, you know the type is `sha512crypt`.

In order to use hashcat, we need to put all our hash information into it's own file, which would look like this - 
```
$6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1
```
Note that we exclude the `username` and `:other_data` - we don't need this to crack the hash.

Now we can run our command - 
```
hashcat -m 1800 <hash file> <wordlist>
```
In my case, that would be 
```
hashcat -m 1800 ./hash.txt ./rockyou.txt
```

My VM complained about not using a native Intel OpenCL runtime. No idea what that means, but i'll use --force to override it.

Luckily, no blue smoke!

We get the following output - 
```
Dictionary cache built:
* Filename..: ./rockyou.txt
* Passwords.: 14344391
* Bytes.....: 139921497
* Keyspace..: 14344384
* Runtime...: 10 secs

$6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1:rainbow
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Type........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0...N6aYP1
Time.Started.....: Sun May 10 00:41:53 2020 (11 secs)
Time.Estimated...: Sun May 10 00:42:04 2020 (0 secs)
Guess.Base.......: File (./rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:       18 H/s (7.38ms) @ Accel:64 Loops:16 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 192/14344384 (0.00%)
Rejected.........: 0/192 (0.00%)
Restore.Point....: 128/14344384 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4992-5000
Candidates.#1....: carolina -> november

Started: Sun May 10 00:40:42 2020
Stopped: Sun May 10 00:42:05 2020
```
It looks like our password is `rainbow` - Let's try it!

It's correct! That's it for Day 3!