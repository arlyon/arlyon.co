---
title: "GSOC18: Reverse Engineering AirPlay for VLC"
date: 2018-08-14T12:38:31+01:00
draft: false
tags: ["c", "open-source"]
---

I discovered Google Summer of Code quite late. Scrambling
together my applications made for a hectic weekend. I had a goal in
mind but, as a contingency, used up all three proposals. A few days
later an email pinged into my inbox from a familiar name and I was
welcomed aboard.

I was bringing AirPlay support to VLC.

## The Goal

In VLC 3.0 there was a considerable effort made that paved the way for
my project. Support for Chromecast, a competing protocol, was added
along with bonjour monitoring. The next iteration of this now that
the foundations were laid was supporting an additional protocol. In
addition to this, I wanted to try and implement streaming the other way:
to an instance of VLC. This would mean first reverse engineering the
protocol as well as implementating AirPlay's pairing process.
From there it is "easy" as you only need to host the media file and send
the URL to the device.

## Process

The first step was documenting the protocol which meant a good few
days in Wireshark. The result was compiled in [the project wiki]
(https://code.videolan.org/GSoC2018/arlyon/vlc/wikis/home) and served
as a reference for the rest of the project. This was continually refined
during the course of the summer as new information replaced the old,
or more detailed data was uncovered.

The next step was pairing with the TV.

When we set out neither myself nor my mentor anticipated how
cryptographically heavy this project would end up being. The reverse
engineering was fairly simple with a few projects that I could learn
from that had partially implemented it. The main problem was
translating these high-level implementations into low-level manipulation
of cryptographic primitives. Some of the libraries we used had difficult
to reason with APIs and counterintuitive quirks that made getting it
working hard. With no indication of progress from the Apple
TV it was hard to determine whether any progress made was even in the right direction.

So after a long couple of weeks of entirely unforeseen work, the first
signs of progress started to appear. The Apple TV would respond to the
pairing requests and we were able to send arbitrary URIs to be displayed
on the screen.

From there we, like in the Chromecast module, had to simulate a sub
stream and create a web server to serve the playlist files for the
Apple TV.

The result of this project can be found at the link below.

<center>[GSoC2018/arlyon/vlc - airplay/final](https://code.videolan.org/GSoC2018/arlyon/vlc/commits/airplay/final)</center>

## Reflection

The scope of the project was reduced as we realized just how much work
the pairing process would take. As such, I have not managed to
implement casting to VLC over AirPlay, only from VLC over AirPlay.

However, I have learned an inconceivable amount the last few months and
the delays were not due to lack of progress. Any work that didn't
result in direct progress was instead contributing towards my education.
I have read multiple books on cryptography, code refactoring,
concurrency and threading, and POSIX. Beyond that, I have learned
a great deal about the C programming language (and c++ too), memory
management, networking, sockets, file descriptors, multithreading,
ECC, SRP6, and on and on not to mention the good habits. Although I'm
worried I'll start using Hungarian notation all the time!

I originally applied as a student looking to get into open source and
I have achieved all of my goals. My original draw to this project was
due to my long history with the software and it feels great to be given
the opportunity to give back some of the hours I spent using this
software. I plan on continuing work on VLC in the future.

## Acknowledgements

I'd like to thank

- Steve Lhomme, my mentor, for answering my endless questions, dealing with my odd schedule, and keeping me on track
- JB, the rest of the people at VideoLabs, and the rest of the contributors for maintaining such a great project
- [funtax](https://github.com/funtax) for their high-level AirPlay pairing implementation saving me many hours
- [cocagne](https://github.com/cocagne) for their implementation of SRP6 from which I based my version

Lastly, my girlfriend and family, with whom I have celebrated the good times and persevered the bad times.