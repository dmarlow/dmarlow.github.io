---
layout: post
title: "Creating a Simple SMS App - HackerNews"
comments: true
tags: [sms,mqtt,iot,hn,hackernews]
---

As I was sitting around I wondered what the top post in HackerNews was. Sure, I could have installed one of hundreds, or thousands, of apps or news readers. I could have even fired up my mobile browser or navigated via a bookmark. However, I wanted to see what it'd take to create an SMS wrapper around HN. Why? More like, why not?!

## Here it Goes
First things first, I create an SMS app on [SMSCMD](http://www.smscmd.net/) like [this blog post](/2014/07/sms-enabling-your-app-or-device.html) describes. I basically support these types of commands:

* hn top \[1\-3\] \- ex: _hn top_ or _hn top 2_
* hn new \[1\-3\] \- ex: _hn new_ or _hn new 3_

If the count is omitted, only one result is returned. Try it now, send a HackerNews command \(_hn top_\) to _\+13109064555_.

The body parsing and HN news fetching is basically all there is. Here's my regular expression to parse the command \(I actually don't receive the text "hn" as that's the SMS app name and is forwarded to that handler\):

<script src="https://gist.github.com/dmarlow/2ea49c00ebc2e4e8e951.js"></script>

I use [HackerSharp](https://github.com/neilkennedy/HackerSharp/) to fetch the HackerNews articles. This is what my MQTT message received handler looks like:

<script src="https://gist.github.com/dmarlow/9b584475b025c2bc277a.js"></script>

That's all there is to it. The only special sauce there is shortening URLs so I don't waste precious SMS space and _"x\.Title\.Head\(30\)"_ \(it gets the first 30 characters and adds ellipsis if needed\). Here's what I got when I sent _hn top_ when writing this.

	MiniLock \- File encryption...
	http://goo.gl/7XRRKB
	76 pts \- poolpool | 25 comments

\- Dariel Marlow
