---
layout: post
title: "SMS Enabling Your App/Device"
comments: true
tags: [sms,mqtt,iot]
---

I recently wrote a small app for my brother-in-law. It's a simple console app that drives selenium to automate a web browser. Great, it works and it works pretty well. It sits there day and night doing its thing. Soon thereafter, my brother in law asked me to tweak it, to turn it off, to pause it or change some behavior. After thinking about this, I realized there must be a better way! I didn't want to write an app, wait for it to be published, then get him to install it. Let alone writing multiple versions to put into multiple app stores should he change phones. The next alternative was to make a web interface for it. I thought that it seemed a bit heavy. Plus, I would have to open a firewall port and add security. It just didn't feel right. Then, I realized that he already has something that can communicate with the app, his phone. Why not send a text message and control the app directly? So that's exactly what I decided to build and make available to others as well.

## Introducing SMSCMD

[SMSCMD](http://www.smscmd.net/) allows you to control your app (or Arduino/Raspberry Pi/Gadgeteer/etc. device) remotely via SMS. It's super easy to setup. There's a free version that's more than capable of handling simple things that don't need attention ever day.

## How it Works

It's basically an MQTT server that can take requests via SMS. I use Twilio to capture SMS messages via HTTP, then I dump those into MQTT.

![image](/images/2014-07/1_visio_lg.png)

## Getting Started
Still with me? Great! First, you need to create an SMS app on SMSCMD to begin the process. 

Names must be unique. The SMS Password can be left blank. This is an added level of control most users probably won't need or use. Specify your MQTT username and password. This is what you'll use to connect to the MQTT server.

![image](/images/2014-07/1_add_lg.png)

There's a section that shows the connection information:
Server: q.smscmd.net
Port: 1883
Client ID: weather-app
Use the username and password specified
Sub topic: weather-app/cmds
Pub topic: weather-app/reply

When you click on the connection example, you're shown this:

<script src="https://gist.github.com/dmarlow/4784d2080db9c7a51590.js"></script>

It's basically a "ready to copy & paste" python version. A C# version is available on [GitHub](https://github.com/dmarlow/Sms2Mqtt/blob/master/src/Sms2Mqtt/Program.cs).

Finally, there's a section to configure what you want your app/device to receive. Depending on your plan, you can choose only the phone numbers that are allowed to send commands. You can test things out before hitting "Add" as well.

![image](/images/2014-07/1_add2_lg.png)

## Responding to a Command

You'll notice in both examples (Python and C#) that there's something in the handler for when command, or message, arrives to reply. The format is important. You must reply and specify the command ID you received. This is to ensure things are sent back to the proper source. Both examples are basically an echo application; it responds back the same thing it received.

<script src="https://gist.github.com/dmarlow/fe4bdc40cfb36c178048.js"></script>

The reply should be send to "<command name>/reply" topic. The format must be an object with the command ID and body properties. { "cmdId": <whatever was sent>, "body": <whatever you wish to send back to the sender via SMS> }.

That JSON object needs to be serialized and encoded into bytes (depending on your MQTT client). In C# it looks like this:

<script src="https://gist.github.com/dmarlow/25b4ae6b93b9eac45d66.js"></script>

![image](/images/2014-07/1_home_lg.png)

I'll leave it up to you to find out what happens when you send something the site doesn't understand.

Thanks for reading!

\- Dariel Marlow
