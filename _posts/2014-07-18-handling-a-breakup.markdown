---
layout: post
title: "Handling a Breakup"
comments: true
tags: [sms,mqtt,iot]
---

Let's face it, everything must come to an end. Even that MQTT connection of yours will eventually break up with you disconnect. In the world of MQTT, the client connects to the server. So, in the event of an inevitable disconnect, what should your client do? Grab some ice-cream and hunker down in bed waiting for the server to come crawling back? No, it's got to go out into the world and figure things out. So, how should it do it?

## The Solution
The answer is: well, it depends. What sort of client are you? One who is power/CPU constrained or not? One who crumbles at the sight of a disconnection? Here are some strategies for handling disconnection.

### Naive Reconnect
We could do something like this:

	Create client
	Register callbacks

	TryConnect:
	  Loop forever
		If not connected
		  Connect
		Sleep X

	TryConnect() // Attempt connection
	
That loop forever step could be a in its own thread, but there are still issues with this. First, we will check every X time interval and needlessly spin the CPU and use resources. Even in a power/CPU rich environment, this isn't the most ideal.

### Self-Aware Reconnect
After some introspection and self-awareness, we reach the conclusion that we can do better. How about this:

	Create client
	Register callbacks

	OnDisconnect Callback:
	  TryConnect()

	TryConnect:
	  Loop while not connected
		Connect
		Sleep X

	TryConnect() // Attempt connection

That's slightly better. We don't needlessly check unless we have to. We only loop when we're trying to connect. We use the disconnect callback handler to worry about connecting. This is essentially what's in the Sms2Mqtt GitHub repo.

### Zenful

If we're a power/CPU critical, and packet conscious, app/device, we may need to take better care of how we do things. Spinning endlessly while trying to connect is a noble goal, but we can kill ourselves trying. We should probably introduce some sort of exponential back-off. This allows us to try to connect, and if the server isn't answering our calls, we don't want to seem overly eager, or desperate. It might give the wrong impression and we'll just get exhausted.

	Create client
	Register callbacks

	OnDisconnect Callback:
	  TryConnect()

	TryConnect:
	  Interval = X
	  Loop while not connected
		Interval = Min(Interval * 2, Limit)
		Connect
		Sleep Interval

	TryConnect() // Attempt connection

I think this gets me to a happy place. My client spends CPU cycles more efficiently and takes a break when it can't connect. I think a perfectly valid interval to begin with is 1 second. From there, it jumps up to 2, 4, 8, 16, 32, then 64 seconds (where I'd place the limit).

Until next time,

\- Dariel Marlow
