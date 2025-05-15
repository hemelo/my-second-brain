>[!WARNING]
>This is a mirror from https://beej.us/guide/bgnet/html/split/intro.html
>I'm not the owner of this guide.

## Intro
-----

Hey! Socket programming got you down? Is this stuff just a little too difficult to figure out from the `man` pages? You want to do cool Internet programming, but you don’t have time to wade through a gob of `struct`s trying to figure out if you have to call `bind()` before you `connect()`, etc., etc.

Well, guess what! I’ve already done this nasty business, and I’m dying to share the information with everyone! You’ve come to the right place. This document should give the average competent C programmer the edge s/he needs to get a grip on this networking noise.

And check it out: I’ve finally caught up with the future (just in the nick of time, too!) and have updated the Guide for IPv6! Enjoy!

## Audience
--------

This document has been written as a tutorial, not a complete reference. It is probably at its best when read by individuals who are just starting out with socket programming and are looking for a foothold. It is certainly not the _complete and total_ guide to sockets programming, by any means.

Hopefully, though, it’ll be just enough for those man pages to start making sense… `:-)`

## Platform and Compiler
---------------------

The code contained within this document was compiled on a Linux PC using Gnu’s `gcc` compiler. It should, however, build on just about any platform that uses `gcc`. Naturally, this doesn’t apply if you’re programming for Windows—see the [section on Windows programming](https://beej.us/guide/bgnet/html/split/intro.html#windows), below.

## Official Homepage and Books For Sale
------------------------------------

This official location of this document is:

*   [`https://beej.us/guide/bgnet/`](https://beej.us/guide/bgnet/)

There you will also find example code and translations of the guide into various languages.

To buy nicely bound print copies (some call them “books”), visit:

*   [`https://beej.us/guide/url/bgbuy`](https://beej.us/guide/url/bgbuy)

I’ll appreciate the purchase because it helps sustain my document-writing lifestyle!

## Note for Windows Programmers
----------------------------

At this point in the guide, historically, I’ve done a bit of bagging on Windows, simply due to the fact that I don’t like it very much. But I should really be fair and tell you that Windows has a huge install base and is obviously a perfectly fine operating system.

They say absence makes the heart grow fonder, and in this case, I believe it to be true. (Or maybe it’s age.) But what I can say is that after a decade-plus of not using Microsoft OSes for my personal work, I’m much happier! As such, I can sit back and safely say, “Sure, feel free to use Windows!” …OK yes, it does make me grit my teeth to say that.

So I still encourage you to try [Linux](https://www.linux.com/)[1](https://beej.us/guide/bgnet/html/split/more-references.html#fn1), [BSD](https://bsd.org/)[2](https://beej.us/guide/bgnet/html/split/more-references.html#fn2), or some flavor of Unix, instead.

But people like what they like, and you Windows folk will be pleased to know that this information is generally applicable to you guys, with a few minor changes, if any.

Another thing that you should strongly consider is the [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/)[3](https://beej.us/guide/bgnet/html/split/more-references.html#fn3). This basically allows you to install a Linux VM-ish thing on Windows 10. That will also definitely get you situated, and you’ll be able to build and run these programs as is.

One cool thing you can do is install [Cygwin](https://cygwin.com/)[4](https://beej.us/guide/bgnet/html/split/more-references.html#fn4), which is a collection of Unix tools for Windows. I’ve heard on the grapevine that doing so allows all these programs to compile unmodified, but I’ve never tried it.

But some of you might want to do things the Pure Windows Way. That’s very gutsy of you, and this is what you have to do: run out and get Unix immediately! No, no—I’m kidding. I’m supposed to be Windows-friendly(er) these days…

This is what you’ll have to do: first, ignore pretty much all of the system header files I mention in here. Instead, include:

```
#include <winsock2.h>
#include <ws2tcpip.h>
```


`winsock2` is the “new” (circa 1994) version of the Windows socket library.

Unfortunately, if you include `windows.h`, it automatically pulls in the older `winsock.h` (version 1) header file which conflicts with `winsock2.h`! Fun times.

So if you have to include `windows.h`, you need to define a macro to get it to _not_ include the older header:

```
#define WIN32_LEAN_AND_MEAN  // Say this...

#include <windows.h>         // And now we can include that.
#include <winsock2.h>        // And this.
```


Wait! You also have to make a call to `WSAStartup()` before doing anything else with the sockets library. You pass in the Winsock version you desire to this function (e.g. version 2.2). And then you can check the result to make sure that version is available.

The code to do that looks something like this:

```
#include <winsock2.h>

{
    WSADATA wsaData;

    if (WSAStartup(MAKEWORD(2, 2), &wsaData) != 0) {
        fprintf(stderr, "WSAStartup failed.\n");
        exit(1);
    }

    if (LOBYTE(wsaData.wVersion) != 2 ||
        HIBYTE(wsaData.wVersion) != 2)
    {
        fprintf(stderr,"Versiion 2.2 of Winsock is not available.\n");
        WSACleanup();
        exit(2);
    }
```


Note that call to `WSACleanup()` in there. That’s what you want to call when you’re done with the Winsock library.

You also have to tell your compiler to link in the Winsock library, called `ws2_32.lib` for Winsock 2. Under VC++, this can be done through the `Project` menu, under `Settings...`. Click the `Link` tab, and look for the box titled “Object/library modules”. Add “ws2\_32.lib” (or whichever lib is your preference) to that list.

Or so I hear.

Once you do that, the rest of the examples in this tutorial should generally apply, with a few exceptions. For one thing, you can’t use `close()` to close a socket—you need to use `closesocket()`, instead. Also, `select()` only works with socket descriptors, not file descriptors (like `0` for `stdin`).

There is also a socket class that you can use, [`CSocket`](https://learn.microsoft.com/en-us/cpp/mfc/reference/csocket-class?view=msvc-170) Check your compiler’s help pages for more information.

To get more information about Winsock, [check out the official page at Microsoft](https://learn.microsoft.com/en-us/windows/win32/winsock/windows-sockets-start-page-2).

Finally, I hear that Windows has no `fork()` system call which is, unfortunately, used in some of my examples. Maybe you have to link in a POSIX library or something to get it to work, or you can use `CreateProcess()` instead. `fork()` takes no arguments, and `CreateProcess()` takes about 48 billion arguments. If you’re not up to that, the `CreateThread()` is a little easier to digest…unfortunately a discussion about multithreading is beyond the scope of this document. I can only talk about so much, you know!

Extra finally, Steven Mitchell has [ported a number of the examples](https://www.tallyhawk.net/WinsockExamples/)[5](https://beej.us/guide/bgnet/html/split/more-references.html#fn5) to Winsock. Check that stuff out.

## What is a socket?
-----------------

You hear talk of “sockets” all the time, and perhaps you are wondering just what they are exactly. Well, they’re this: a way to speak to other programs using standard Unix file descriptors.

What?

Ok—you may have heard some Unix hacker state, “Jeez, _everything_ in Unix is a file!” What that person may have been talking about is the fact that when Unix programs do any sort of I/O, they do it by reading or writing to a file descriptor. A file descriptor is simply an integer associated with an open file. But (and here’s the catch), that file can be a network connection, a FIFO, a pipe, a terminal, a real on-the-disk file, or just about anything else. Everything in Unix _is_ a file! So when you want to communicate with another program over the Internet you’re gonna do it through a file descriptor, you’d better believe it.

“Where do I get this file descriptor for network communication, Mr. Smarty-Pants?” is probably the last question on your mind right now, but I’m going to answer it anyway: You make a call to the `socket()` system routine. It returns the socket descriptor, and you communicate through it using the specialized `send()` and `recv()` ([`man send`](https://beej.us/guide/bgnet/html/split/man-pages.html#sendman), [`man recv`](https://beej.us/guide/bgnet/html/split/man-pages.html#recvman)) socket calls.

“But, hey!” you might be exclaiming right about now. “If it’s a file descriptor, why in the name of Neptune can’t I just use the normal `read()` and `write()` calls to communicate through the socket?” The short answer is, “You can!” The longer answer is, “You can, but `send()` and `recv()` offer much greater control over your data transmission.”

What next? How about this: there are all kinds of sockets. There are DARPA Internet addresses (Internet Sockets), path names on a local node (Unix Sockets), CCITT X.25 addresses (X.25 Sockets that you can safely ignore), and probably many others depending on which Unix flavor you run. This document deals only with the first: Internet Sockets.

### Two Types of Internet Sockets
-----------------------------

What’s this? There are two types of Internet sockets? Yes. Well, no. I’m lying. There are more, but I didn’t want to scare you. I’m only going to talk about two types here. Except for this sentence, where I’m going to tell you that “Raw Sockets” are also very powerful and you should look them up.

All right, already. What are the two types? One is “Stream Sockets”; the other is “Datagram Sockets”, which may hereafter be referred to as “`SOCK_STREAM`” and “`SOCK_DGRAM`”, respectively. Datagram sockets are sometimes called “connectionless sockets”. (Though they can be `connect()`’d if you really want. See [`connect()`](https://beej.us/guide/bgnet/html/split/system-calls-or-bust.html#connect), below.)

Stream sockets are reliable two-way connected communication streams. If you output two items into the socket in the order “1, 2”, they will arrive in the order “1, 2” at the opposite end. They will also be error-free. I’m so certain, in fact, they will be error-free, that I’m just going to put my fingers in my ears and chant _la la la la_ if anyone tries to claim otherwise.

What uses stream sockets? Well, you may have heard of the `telnet` or `ssh` applications, yes? They use stream sockets. All the characters you type need to arrive in the same order you type them, right? Also, web browsers use the Hypertext Transfer Protocol (HTTP) which uses stream sockets to get pages. Indeed, if you telnet to a web site on port 80, and type “`GET / HTTP/1.0`” and hit RETURN twice, it’ll dump the HTML back at you!

> If you don’t have `telnet` installed and don’t want to install it, or your `telnet` is being picky about connecting to clients, the guide comes with a `telnet`\-like program called [`telnot`](https://beej.us/guide/bgnet/source/examples/telnot.c)[7](https://beej.us/guide/bgnet/html/split/more-references.html#fn7). This should work well for all the needs of the guide. (Note that telnet is actually a [spec’d networking protocol](https://tools.ietf.org/html/rfc854)[8](https://beej.us/guide/bgnet/html/split/more-references.html#fn8), and `telnot` doesn’t implement this protocol at all.)

How do stream sockets achieve this high level of data transmission quality? They use a protocol called “The Transmission Control Protocol”, otherwise known as “TCP” (see [RFC 793](https://tools.ietf.org/html/rfc793)[9](https://beej.us/guide/bgnet/html/split/more-references.html#fn9) for extremely detailed info on TCP). TCP makes sure your data arrives sequentially and error-free. You may have heard “TCP” before as the better half of “TCP/IP” where “IP” stands for “Internet Protocol” (see [RFC 791](https://tools.ietf.org/html/rfc791)[10](https://beej.us/guide/bgnet/html/split/more-references.html#fn10)). IP deals primarily with Internet routing and is not generally responsible for data integrity.

Cool. What about Datagram sockets? Why are they called connectionless? What is the deal, here, anyway? Why are they unreliable? Well, here are some facts: if you send a datagram, it may arrive. It may arrive out of order. If it arrives, the data within the packet will be error-free.

Datagram sockets also use IP for routing, but they don’t use TCP; they use the “User Datagram Protocol”, or “UDP” (see [RFC 768](https://tools.ietf.org/html/rfc768)[11](https://beej.us/guide/bgnet/html/split/more-references.html#fn11)).

Why are they connectionless? Well, basically, it’s because you don’t have to maintain an open connection as you do with stream sockets. You just build a packet, slap an IP header on it with destination information, and send it out. No connection needed. They are generally used either when a TCP stack is unavailable or when a few dropped packets here and there don’t mean the end of the Universe. Sample applications: `tftp` (trivial file transfer protocol, a little brother to FTP), `dhcpcd` (a DHCP client), multiplayer games, streaming audio, video conferencing, etc.

“Wait a minute! `tftp` and `dhcpcd` are used to transfer binary applications from one host to another! Data can’t be lost if you expect the application to work when it arrives! What kind of dark magic is this?”

Well, my human friend, `tftp` and similar programs have their own protocol on top of UDP. For example, the tftp protocol says that for each packet that gets sent, the recipient has to send back a packet that says, “I got it!” (an “ACK” packet). If the sender of the original packet gets no reply in, say, five seconds, he’ll re-transmit the packet until he finally gets an ACK. This acknowledgment procedure is very important when implementing reliable `SOCK_DGRAM` applications.

For unreliable applications like games, audio, or video, you just ignore the dropped packets, or perhaps try to cleverly compensate for them. (Quake players will know the manifestation this effect by the technical term: _accursed lag_. The word “accursed”, in this case, represents any extremely profane utterance.)

Why would you use an unreliable underlying protocol? Two reasons: speed and speed. It’s way faster to fire-and-forget than it is to keep track of what has arrived safely and make sure it’s in order and all that. If you’re sending chat messages, TCP is great; if you’re sending 40 positional updates per second of the players in the world, maybe it doesn’t matter so much if one or two get dropped, and UDP is a good choice.

### Network Theory
-------------------------------------

Since I just mentioned layering of protocols, it’s time to talk about how networks really work, and to show some examples of how `SOCK_DGRAM` packets are built. Practically, you can probably skip this section. It’s good background, however.

Hey, kids, it’s time to learn about _Data Encapsulation_! This is very very important. It’s so important that you might just learn about it if you take the networks course here at Chico State `;-)`. Basically, it says this: a packet is born, the packet is wrapped (“encapsulated”) in a header (and rarely a footer) by the first protocol (say, the TFTP protocol), then the whole thing (TFTP header included) is encapsulated again by the next protocol (say, UDP), then again by the next (IP), then again by the final protocol on the hardware (physical) layer (say, Ethernet).

When another computer receives the packet, the hardware strips the Ethernet header, the kernel strips the IP and UDP headers, the TFTP program strips the TFTP header, and it finally has the data.

Now I can finally talk about the infamous _Layered Network Model_ (aka “ISO/OSI”). This Network Model describes a system of network functionality that has many advantages over other models. For instance, you can write sockets programs that are exactly the same without caring how the data is physically transmitted (serial, thin Ethernet, AUI, whatever) because programs on lower levels deal with it for you. The actual network hardware and topology is transparent to the socket programmer.

Without any further ado, I’ll present the layers of the full-blown model. Remember this for network class exams:

*   Application
*   Presentation
*   Session
*   Transport
*   Network
*   Data Link
*   Physical

The Physical Layer is the hardware (serial, Ethernet, etc.). The Application Layer is just about as far from the physical layer as you can imagine—it’s the place where users interact with the network.

Now, this model is so general you could probably use it as an automobile repair guide if you really wanted to. A layered model more consistent with Unix might be:

*   Application Layer (_telnet, ftp, etc._)
*   Host-to-Host Transport Layer (_TCP, UDP_)
*   Internet Layer (_IP and routing_)
*   Network Access Layer (_Ethernet, wi-fi, or whatever_)

At this point in time, you can probably see how these layers correspond to the encapsulation of the original data.

See how much work there is in building a simple packet? Jeez! And you have to type in the packet headers yourself using “`cat`”! Just kidding. All you have to do for stream sockets is `send()` the data out. All you have to do for datagram sockets is encapsulate the packet in the method of your choosing and `sendto()` it out. The kernel builds the Transport Layer and Internet Layer on for you and the hardware does the Network Access Layer. Ah, modern technology.

So ends our brief foray into network theory. Oh yes, I forgot to tell you everything I wanted to say about routing: nothing! That’s right, I’m not going to talk about it at all. The router strips the packet to the IP header, consults its routing table, _blah blah blah_. Check out the [IP RFC](https://tools.ietf.org/html/rfc791)[12](https://beej.us/guide/bgnet/html/split/more-references.html#fn12) if you really really care. If you never learn about it, well, you’ll live.
## IP Addresses, `struct`s, and Data Munging
-----------------------------------------

Here’s the part of the game where we get to talk code for a change.

But first, let’s discuss more non-code! Yay! First I want to talk about IP addresses and ports for just a tad so we have that sorted out. Then we’ll talk about how the sockets API stores and manipulates IP addresses and other data.

### IP Addresses, versions 4 and 6
------------------------------

In the good old days back when Ben Kenobi was still called Obi Wan Kenobi, there was a wonderful network routing system called The Internet Protocol Version 4, also called IPv4. It had addresses made up of four bytes (A.K.A. four “octets”), and was commonly written in “dots and numbers” form, like so: `192.0.2.111`.

You’ve probably seen it around.

In fact, as of this writing, virtually every site on the Internet uses IPv4.

Everyone, including Obi Wan, was happy. Things were great, until some naysayer by the name of Vint Cerf warned everyone that we were about to run out of IPv4 addresses!

(Besides warning everyone of the Coming IPv4 Apocalypse Of Doom And Gloom, [Vint Cerf](https://en.wikipedia.org/wiki/Vint_Cerf)[13](https://beej.us/guide/bgnet/html/split/more-references.html#fn13) is also well-known for being The Father Of The Internet. So I really am in no position to second-guess his judgment.)

Run out of addresses? How could this be? I mean, there are like billions of IP addresses in a 32-bit IPv4 address. Do we really have billions of computers out there?

Yes.

Also, in the beginning, when there were only a few computers and everyone thought a billion was an impossibly large number, some big organizations were generously allocated millions of IP addresses for their own use. (Such as Xerox, MIT, Ford, HP, IBM, GE, AT&T, and some little company called Apple, to name a few.)

In fact, if it weren’t for several stopgap measures, we would have run out a long time ago.

But now we’re living in an era where we’re talking about every human having an IP address, every computer, every calculator, every phone, every parking meter, and (why not) every puppy dog, as well.

And so, IPv6 was born. Since Vint Cerf is probably immortal (even if his physical form should pass on, heaven forbid, he is probably already existing as some kind of hyper-intelligent [ELIZA](https://en.wikipedia.org/wiki/ELIZA)[14](https://beej.us/guide/bgnet/html/split/more-references.html#fn14) program out in the depths of the Internet2), no one wants to have to hear him say again “I told you so” if we don’t have enough addresses in the next version of the Internet Protocol.

What does this suggest to you?

That we need a _lot_ more addresses. That we need not just twice as many addresses, not a billion times as many, not a thousand trillion times as many, but _79 MILLION BILLION TRILLION times as many possible addresses!_ That’ll show ’em!

You’re saying, “Beej, is that true? I have every reason to disbelieve large numbers.” Well, the difference between 32 bits and 128 bits might not sound like a lot; it’s only 96 more bits, right? But remember, we’re talking powers here: 32 bits represents some 4 billion numbers (232), while 128 bits represents about 340 trillion trillion trillion numbers (for real, 2128). That’s like a million IPv4 Internets for _every single star in the Universe_.

Forget this dots-and-numbers look of IPv4, too; now we’ve got a hexadecimal representation, with each two-byte chunk separated by a colon, like this:

```
2001:0db8:c9d2:aee5:73e3:934a:a5ae:9551
```


That’s not all! Lots of times, you’ll have an IP address with lots of zeros in it, and you can compress them between two colons. And you can leave off leading zeros for each byte pair. For instance, each of these pairs of addresses are equivalent:

```
2001:0db8:c9d2:0012:0000:0000:0000:0051
2001:db8:c9d2:12::51

2001:0db8:ab00:0000:0000:0000:0000:0000
2001:db8:ab00::

0000:0000:0000:0000:0000:0000:0000:0001
::1
```


The address `::1` is the _loopback address_. It always means “this machine I’m running on now”. In IPv4, the loopback address is `127.0.0.1`.

Finally, there’s an IPv4-compatibility mode for IPv6 addresses that you might come across. If you want, for example, to represent the IPv4 address `192.0.2.33` as an IPv6 address, you use the following notation: “`::ffff:192.0.2.33`”.

We’re talking serious fun.

In fact, it’s such serious fun, that the Creators of IPv6 have quite cavalierly lopped off trillions and trillions of addresses for reserved use, but we have so many, frankly, who’s even counting anymore? There are plenty left over for every man, woman, child, puppy, and parking meter on every planet in the galaxy. And believe me, every planet in the galaxy has parking meters. You know it’s true.

#### Subnets

For organizational reasons, it’s sometimes convenient to declare that “this first part of this IP address up through this bit is the _network portion_ of the IP address, and the remainder is the _host portion_.

For instance, with IPv4, you might have `192.0.2.12`, and we could say that the first three bytes are the network and the last byte was the host. Or, put another way, we’re talking about host `12` on network `192.0.2.0` (see how we zero out the byte that was the host).

And now for more outdated information! Ready? In the Ancient Times, there were “classes” of subnets, where the first one, two, or three bytes of the address was the network part. If you were lucky enough to have one byte for the network and three for the host, you could have 24 bits-worth of hosts on your network (16 million or so). That was a “Class A” network. On the opposite end was a “Class C”, with three bytes of network, and one byte of host (256 hosts, minus a couple that were reserved).

So as you can see, there were just a few Class As, a huge pile of Class Cs, and some Class Bs in the middle.

The network portion of the IP address is described by something called the _netmask_, which you bitwise-AND with the IP address to get the network number out of it. The netmask usually looks something like `255.255.255.0`. (E.g. with that netmask, if your IP is `192.0.2.12`, then your network is `192.0.2.12` AND `255.255.255.0` which gives `192.0.2.0`.)

Unfortunately, it turned out that this wasn’t fine-grained enough for the eventual needs of the Internet; we were running out of Class C networks quite quickly, and we were most definitely out of Class As, so don’t even bother to ask. To remedy this, The Powers That Be allowed for the netmask to be an arbitrary number of bits, not just 8, 16, or 24. So you might have a netmask of, say `255.255.255.252`, which is 30 bits of network, and 2 bits of host allowing for four hosts on the network. (Note that the netmask is _ALWAYS_ a bunch of 1-bits followed by a bunch of 0-bits.)

But it’s a bit unwieldy to use a big string of numbers like `255.192.0.0` as a netmask. First of all, people don’t have an intuitive idea of how many bits that is, and secondly, it’s really not compact. So the New Style came along, and it’s much nicer. You just put a slash after the IP address, and then follow that by the number of network bits in decimal. Like this: `192.0.2.12/30`.

Or, for IPv6, something like this: `2001:db8::/32` or `2001:db8:5413:4028::9db9/64`.

#### Port Numbers

If you’ll kindly remember, I presented you earlier with the [Layered Network Model](https://beej.us/guide/bgnet/html/split/what-is-a-socket.html#lowlevel) which had the Internet Layer (IP) split off from the Host-to-Host Transport Layer (TCP and UDP). Get up to speed on that before the next paragraph.

Turns out that besides an IP address (used by the IP layer), there is another address that is used by TCP (stream sockets) and, coincidentally, by UDP (datagram sockets). It is the _port number_. It’s a 16-bit number that’s like the local address for the connection.

Think of the IP address as the street address of a hotel, and the port number as the room number. That’s a decent analogy; maybe later I’ll come up with one involving the automobile industry.

Say you want to have a computer that handles incoming mail AND web services—how do you differentiate between the two on a computer with a single IP address?

Well, different services on the Internet have different well-known port numbers. You can see them all in [the Big IANA Port List](https://www.iana.org/assignments/port-numbers)[15](https://beej.us/guide/bgnet/html/split/more-references.html#fn15) or, if you’re on a Unix box, in your `/etc/services` file. HTTP (the web) is port 80, telnet is port 23, SMTP is port 25, the game [DOOM](https://en.wikipedia.org/wiki/Doom_\(1993_video_game\))[16](https://beej.us/guide/bgnet/html/split/more-references.html#fn16) used port 666, etc. and so on. Ports under 1024 are often considered special, and usually require special OS privileges to use.

And that’s about it!

### Byte Order
----------

By Order of the Realm! There shall be two byte orderings, hereafter to be known as Lame and Magnificent!

I joke, but one really is better than the other. `:-)`

There really is no easy way to say this, so I’ll just blurt it out: your computer might have been storing bytes in reverse order behind your back. I know! No one wanted to have to tell you.

The thing is, everyone in the Internet world has generally agreed that if you want to represent the two-byte hex number, say `b34f`, you’ll store it in two sequential bytes `b3` followed by `4f`. Makes sense, and, as [Wilford Brimley](https://en.wikipedia.org/wiki/Wilford_Brimley)[17](https://beej.us/guide/bgnet/html/split/more-references.html#fn17) would tell you, it’s the Right Thing To Do. This number, stored with the big end first, is called _Big-Endian_.

Unfortunately, a _few_ computers scattered here and there throughout the world, namely anything with an Intel or Intel-compatible processor, store the bytes reversed, so `b34f` would be stored in memory as the sequential bytes `4f` followed by `b3`. This storage method is called _Little-Endian_.

But wait, I’m not done with terminology yet! The more-sane _Big-Endian_ is also called _Network Byte Order_ because that’s the order us network types like.

Your computer stores numbers in _Host Byte Order_. If it’s an Intel 80x86, Host Byte Order is Little-Endian. If it’s a Motorola 68k, Host Byte Order is Big-Endian. If it’s a PowerPC, Host Byte Order is… well, it depends!

A lot of times when you’re building packets or filling out data structures you’ll need to make sure your two- and four-byte numbers are in Network Byte Order. But how can you do this if you don’t know the native Host Byte Order?

Good news! You just get to assume the Host Byte Order isn’t right, and you always run the value through a function to set it to Network Byte Order. The function will do the magic conversion if it has to, and this way your code is portable to machines of differing endianness.

All righty. There are two types of numbers that you can convert: `short` (two bytes) and `long` (four bytes). These functions work for the `unsigned` variations as well. Say you want to convert a `short` from Host Byte Order to Network Byte Order. Start with “h” for “host”, follow it with “to”, then “n” for “network”, and “s” for “short”: h-to-n-s, or `htons()` (read: “Host to Network Short”).

It’s almost too easy…

You can use every combination of “n”, “h”, “s”, and “l” you want, not counting the really stupid ones. For example, there is NOT a `stolh()` (“Short to Long Host”) function—not at this party, anyway. But there are:


|Function|Description          |
|--------|---------------------|
|htons() |host to network short|
|htonl() |host to network long |
|ntohs() |network to host short|
|ntohl() |network to host long |


Basically, you’ll want to convert the numbers to Network Byte Order before they go out on the wire, and convert them to Host Byte Order as they come in off the wire.

I don’t know of a 64-bit variant, sorry. And if you want to do floating point, check out the section on [Serialization](https://beej.us/guide/bgnet/html/split/slightly-advanced-techniques.html#serialization), far below.

Assume the numbers in this document are in Host Byte Order unless I say otherwise.

### `struct`s
---------

Well, we’re finally here. It’s time to talk about programming. In this section, I’ll cover various data types used by the sockets interface, since some of them are a real bear to figure out.

First the easy one: a socket descriptor. A socket descriptor is the following type:

Just a regular `int`.

Things get weird from here, so just read through and bear with me.

My First Struct™—`struct addrinfo`. This structure is a more recent invention, and is used to prep the socket address structures for subsequent use. It’s also used in host name lookups, and service name lookups. That’ll make more sense later when we get to actual usage, but just know for now that it’s one of the first things you’ll call when making a connection.

```
struct addrinfo {
    int              ai_flags;     // AI_PASSIVE, AI_CANONNAME, etc.
    int              ai_family;    // AF_INET, AF_INET6, AF_UNSPEC
    int              ai_socktype;  // SOCK_STREAM, SOCK_DGRAM
    int              ai_protocol;  // use 0 for "any"
    size_t           ai_addrlen;   // size of ai_addr in bytes
    struct sockaddr *ai_addr;      // struct sockaddr_in or _in6
    char            *ai_canonname; // full canonical hostname

    struct addrinfo *ai_next;      // linked list, next node
};
```


You’ll load this struct up a bit, and then call `getaddrinfo()`. It’ll return a pointer to a new linked list of these structures filled out with all the goodies you need.

You can force it to use IPv4 or IPv6 in the `ai_family` field, or leave it as `AF_UNSPEC` to use whatever. This is cool because your code can be IP version-agnostic.

Note that this is a linked list: `ai_next` points at the next element—there could be several results for you to choose from. I’d use the first result that worked, but you might have different business needs; I don’t know everything, man!

You’ll see that the `ai_addr` field in the `struct addrinfo` is a pointer to a `struct sockaddr`. This is where we start getting into the nitty-gritty details of what’s inside an IP address structure.

You might not usually need to write to these structures; oftentimes, a call to `getaddrinfo()` to fill out your `struct addrinfo` for you is all you’ll need. You _will_, however, have to peer inside these `struct`s to get the values out, so I’m presenting them here.

(Also, all the code written before `struct addrinfo` was invented we packed all this stuff by hand, so you’ll see a lot of IPv4 code out in the wild that does exactly that. You know, in old versions of this guide and so on.)

Some `struct`s are IPv4, some are IPv6, and some are both. I’ll make notes of which are what.

Anyway, the `struct sockaddr` holds socket address information for many types of sockets.

```
struct sockaddr {
    unsigned short    sa_family;    // address family, AF_xxx
    char              sa_data[14];  // 14 bytes of protocol address
}; 
```


`sa_family` can be a variety of things, but it’ll be `AF_INET` (IPv4) or `AF_INET6` (IPv6) for everything we do in this document. `sa_data` contains a destination address and port number for the socket. This is rather unwieldy since you don’t want to tediously pack the address in the `sa_data` by hand.

To deal with `struct sockaddr`, programmers created a parallel structure: `struct sockaddr_in` (“in” for “Internet”) to be used with IPv4.

And _this is the important_ bit: a pointer to a `struct sockaddr_in` can be cast to a pointer to a `struct sockaddr` and vice-versa. So even though `connect()` wants a `struct sockaddr*`, you can still use a `struct sockaddr_in` and cast it at the last minute!

```
// (IPv4 only--see struct sockaddr_in6 for IPv6)

struct sockaddr_in {
    short int          sin_family;  // Address family, AF_INET
    unsigned short int sin_port;    // Port number
    struct in_addr     sin_addr;    // Internet address
    unsigned char      sin_zero[8]; // Same size as struct sockaddr
};
```


This structure makes it easy to reference elements of the socket address. Note that `sin_zero` (which is included to pad the structure to the length of a `struct sockaddr`) should be set to all zeros with the function `memset()`. Also, notice that `sin_family` corresponds to `sa_family` in a `struct sockaddr` and should be set to “`AF_INET`”. Finally, the `sin_port` must be in _Network Byte Order_ (by using `htons()`!)

Let’s dig deeper! You see the `sin_addr` field is a `struct in_addr`. What is that thing? Well, not to be overly dramatic, but it’s one of the scariest unions of all time:

```
// (IPv4 only--see struct in6_addr for IPv6)

// Internet address (a structure for historical reasons)
struct in_addr {
    uint32_t s_addr; // that's a 32-bit int (4 bytes)
};
```


Whoa! Well, it _used_ to be a union, but now those days seem to be gone. Good riddance. So if you have declared `ina` to be of type `struct sockaddr_in`, then `ina.sin_addr.s_addr` references the 4-byte IP address (in Network Byte Order). Note that even if your system still uses the God-awful union for `struct in_addr`, you can still reference the 4-byte IP address in exactly the same way as I did above (this due to `#define`s).

What about IPv6? Similar `struct`s exist for it, as well:

```
// (IPv6 only--see struct sockaddr_in and struct in_addr for IPv4)

struct sockaddr_in6 {
    u_int16_t       sin6_family;   // address family, AF_INET6
    u_int16_t       sin6_port;     // port number, Network Byte Order
    u_int32_t       sin6_flowinfo; // IPv6 flow information
    struct in6_addr sin6_addr;     // IPv6 address
    u_int32_t       sin6_scope_id; // Scope ID
};

struct in6_addr {
    unsigned char   s6_addr[16];   // IPv6 address
};
```


Note that IPv6 has an IPv6 address and a port number, just like IPv4 has an IPv4 address and a port number.

Also note that I’m not going to talk about the IPv6 flow information or Scope ID fields for the moment… this is just a starter guide. `:-)`

Last but not least, here is another simple structure, `struct sockaddr_storage` that is designed to be large enough to hold both IPv4 and IPv6 structures. See, for some calls, sometimes you don’t know in advance if it’s going to fill out your `struct sockaddr` with an IPv4 or IPv6 address. So you pass in this parallel structure, very similar to `struct sockaddr` except larger, and then cast it to the type you need:

```
struct sockaddr_storage {
    sa_family_t  ss_family;     // address family

    // all this is padding, implementation specific, ignore it:
    char      __ss_pad1[_SS_PAD1SIZE];
    int64_t   __ss_align;
    char      __ss_pad2[_SS_PAD2SIZE];
};
```


What’s important is that you can see the address family in the `ss_family` field—check this to see if it’s `AF_INET` or `AF_INET6` (for IPv4 or IPv6). Then you can cast it to a `struct sockaddr_in` or `struct sockaddr_in6` if you wanna.

### IP Addresses, Part Deux
-----------------------

Fortunately for you, there are a bunch of functions that allow you to manipulate IP addresses. No need to figure them out by hand and stuff them in a `long` with the `<<` operator.

First, let’s say you have a `struct sockaddr_in ina`, and you have an IP address “`10.12.110.57`” or “`2001:db8:63b3:1::3490`” that you want to store into it. The function you want to use, `inet_pton()`, converts an IP address in numbers-and-dots notation into either a `struct in_addr` or a `struct in6_addr` depending on whether you specify `AF_INET` or `AF_INET6`. (“`pton`” stands for “presentation to network”—you can call it “printable to network” if that’s easier to remember.) The conversion can be made as follows:

```
struct sockaddr_in sa; // IPv4
struct sockaddr_in6 sa6; // IPv6

inet_pton(AF_INET, "10.12.110.57", &(sa.sin_addr)); // IPv4
inet_pton(AF_INET6, "2001:db8:63b3:1::3490", &(sa6.sin6_addr)); // IPv6
```


(Quick note: the old way of doing things used a function called `inet_addr()` or another function called `inet_aton()`; these are now obsolete and don’t work with IPv6.)

Now, the above code snippet isn’t very robust because there is no error checking. See, `inet_pton()` returns `-1` on error, or 0 if the address is messed up. So check to make sure the result is greater than 0 before using!

All right, now you can convert string IP addresses to their binary representations. What about the other way around? What if you have a `struct in_addr` and you want to print it in numbers-and-dots notation? (Or a `struct in6_addr` that you want in, uh, “hex-and-colons” notation.) In this case, you’ll want to use the function `inet_ntop()` (“ntop” means “network to presentation”—you can call it “network to printable” if that’s easier to remember), like this:

```
// IPv4:

char ip4[INET_ADDRSTRLEN];  // space to hold the IPv4 string
struct sockaddr_in sa;      // pretend this is loaded with something

inet_ntop(AF_INET, &(sa.sin_addr), ip4, INET_ADDRSTRLEN);

printf("The IPv4 address is: %s\n", ip4);


// IPv6:

char ip6[INET6_ADDRSTRLEN]; // space to hold the IPv6 string
struct sockaddr_in6 sa6;    // pretend this is loaded with something

inet_ntop(AF_INET6, &(sa6.sin6_addr), ip6, INET6_ADDRSTRLEN);

printf("The address is: %s\n", ip6);
```


When you call it, you’ll pass the address type (IPv4 or IPv6), the address, a pointer to a string to hold the result, and the maximum length of that string. (Two macros conveniently hold the size of the string you’ll need to hold the largest IPv4 or IPv6 address: `INET_ADDRSTRLEN` and `INET6_ADDRSTRLEN`.)

(Another quick note to mention once again the old way of doing things: the historical function to do this conversion was called `inet_ntoa()`. It’s also obsolete and won’t work with IPv6.)

Lastly, these functions only work with numeric IP addresses—they won’t do any nameserver DNS lookup on a hostname, like “`www.example.com`”. You will use `getaddrinfo()` to do that, as you’ll see later on.

#### Private (Or Disconnected) Networks

Lots of places have a firewall that hides the network from the rest of the world for their own protection. And often times, the firewall translates “internal” IP addresses to “external” (that everyone else in the world knows) IP addresses using a process called _Network Address Translation_, or NAT.

Are you getting nervous yet? “Where’s he going with all this weird stuff?”

Well, relax and buy yourself a non-alcoholic (or alcoholic) drink, because as a beginner, you don’t even have to worry about NAT, since it’s done for you transparently. But I wanted to talk about the network behind the firewall in case you started getting confused by the network numbers you were seeing.

For instance, I have a firewall at home. I have two static IPv4 addresses allocated to me by the DSL company, and yet I have seven computers on the network. How is this possible? Two computers can’t share the same IP address, or else the data wouldn’t know which one to go to!

The answer is: they don’t share the same IP addresses. They are on a private network with 24 million IP addresses allocated to it. They are all just for me. Well, all for me as far as anyone else is concerned. Here’s what’s happening:

If I log into a remote computer, it tells me I’m logged in from 192.0.2.33 which is the public IP address my ISP has provided to me. But if I ask my local computer what its IP address is, it says 10.0.0.5. Who is translating the IP address from one to the other? That’s right, the firewall! It’s doing NAT!

`10.x.x.x` is one of a few reserved networks that are only to be used either on fully disconnected networks, or on networks that are behind firewalls. The details of which private network numbers are available for you to use are outlined in [RFC 1918](https://tools.ietf.org/html/rfc1918)[18](https://beej.us/guide/bgnet/html/split/more-references.html#fn18), but some common ones you’ll see are `10.x.x.x` and `192.168.x.x`, where `x` is 0-255, generally. Less common is `172.y.x.x`, where `y` goes between 16 and 31.

Networks behind a NATing firewall don’t _need_ to be on one of these reserved networks, but they commonly are.

(Fun fact! My external IP address isn’t really `192.0.2.33`. The `192.0.2.x` network is reserved for make-believe “real” IP addresses to be used in documentation, just like this guide! Wowzers!)

IPv6 has private networks, too, in a sense. They’ll start with `fdXX:` (or maybe in the future `fcXX:`), as per [RFC 4193](https://tools.ietf.org/html/rfc4193)[19](https://beej.us/guide/bgnet/html/split/more-references.html#fn19). NAT and IPv6 don’t generally mix, however (unless you’re doing the IPv6 to IPv4 gateway thing which is beyond the scope of this document)—in theory you’ll have so many addresses at your disposal that you won’t need to use NAT any longer. But if you want to allocate addresses for yourself on a network that won’t route outside, this is how to do it.

* * *
## Jumping from IPv4 to IPv6

But I just want to know what to change in my code to get it going with IPv6! Tell me now!

Almost everything in here is something I’ve gone over, above, but it’s the short version for the impatient. (Of course, there is more than this, but this is what applies to the guide.)

*   First of all, try to use [`getaddrinfo()`](https://beej.us/guide/bgnet/html/split/ip-addresses-structs-and-data-munging.html#structs) to get all the `struct sockaddr` info, instead of packing the structures by hand. This will keep you IP version-agnostic, and will eliminate many of the subsequent steps.
    
*   Any place that you find you’re hard-coding anything related to the IP version, try to wrap up in a helper function.
    
*   Change `AF_INET` to `AF_INET6`.
    
*   Change `PF_INET` to `PF_INET6`.
    
*   Change `INADDR_ANY` assignments to `in6addr_any` assignments, which are slightly different:
    
    ```
struct sockaddr_in sa;
struct sockaddr_in6 sa6;

sa.sin_addr.s_addr = INADDR_ANY;  // use my IPv4 address
sa6.sin6_addr = in6addr_any; // use my IPv6 address
```

    
    Also, the value `IN6ADDR_ANY_INIT` can be used as an initializer when the `struct in6_addr` is declared, like so:
    
    ```
struct in6_addr ia6 = IN6ADDR_ANY_INIT;
```

    
*   Instead of `struct sockaddr_in` use `struct sockaddr_in6`, being sure to add “6” to the fields as appropriate (see [`struct`s](https://beej.us/guide/bgnet/html/split/ip-addresses-structs-and-data-munging.html#structs), above). There is no `sin6_zero` field.
    
*   Instead of `struct in_addr` use `struct in6_addr`, being sure to add “6” to the fields as appropriate (see [`struct`s](https://beej.us/guide/bgnet/html/split/ip-addresses-structs-and-data-munging.html#structs), above).
    
*   Instead of `inet_aton()` or `inet_addr()`, use `inet_pton()`.
    
*   Instead of `inet_ntoa()`, use `inet_ntop()`.
    
*   Instead of `gethostbyname()`, use the superior `getaddrinfo()`.
    
*   Instead of `gethostbyaddr()`, use the superior `getnameinfo()` (although `gethostbyaddr()` can still work with IPv6).
    
*   `INADDR_BROADCAST` no longer works. Use IPv6 multicast instead.