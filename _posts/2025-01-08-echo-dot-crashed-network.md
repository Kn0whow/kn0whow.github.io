---
title: "An Echo in the ping: How an Echo Dot crashed a network"
categories: 
  - Networking
tags:
  - Networking
  - Troubleshooting
  - wifi
  - Amazon
toc: true
toc_label: "Contents"
---

Smart speakers, they're great aren't they? You can play music through them, set up routines, ask them random questions, they seem to make life just that little bit easier. Until they don't.

## Setting the scene

The time is 2pm, it's a cold, bleak January afternoon. We get a call from users complaining that the wifi has gone extremely slow, slow enough that they can barely do their work. Ethernet devices seem fine, just wifi users. And they're right, the wifi has gone extremely slow? Some other errors include:
- Users getting disconnected from the wifi, and unable to reconnect.
- Wifi stating 'connected, no internet'.
- DHCP not issuing IP addresses when connecting to the wifi.

Time to investigate.

## Investigation

We begin by doing some ping tests to 8.8.8.8 and some internal IPs, pings on wifi are up in the 200-1000ms ranges, pings through ethernet are the standard 1ms. Very strange.

We think, maybe it's a dodgy switch? We look at which AP affected users are connected to and then test the switches they connect to, but no, the switches appear to be working fine?

Next we speak to more users, and ask them 'Have you been naughty and plugged in a device you shouldn't have?', and everyone says no? We don't believe them of course but we go along with it.

We run ping traces, check wireshark traffic to see if there is some kind of network storm, we reboot switches, unplug APs, check for network loops, and nothing. Nothing makes sense, what could possibly be causing it?

DNS? no. DHCP scope full? no. Black magic? no, we had already covered DNS.

## Finding the cause

It was 4 hours into this investigation, 3 cups of coffee had been made and gone cold, we had to find this issue before our nights operation started and couldn't work without wifi.

So we went back to our theory about users being naughty and plugging things in. We came to the conclusion, that someone, somewhere had done something. So we went round each office, checking ports and scowling at end users until we finally found something interesting...

Sitting there, on the end of a desk, was a shiny new Amazon Echo Dot, playing some of the latest chart music for one of the offices. 'Where did this come from?' we asked, 'I bought it this week' a happy colleague replied. 'When did you set this up?' we asked, 'we set it up this afternoon' they replied. 'What time did you set it up?' we asked, dreading the answer, 'oh, about 1/2pm I think?' they replied. 

We unplugged the speaker, and fought the temptation to throw it like a shot-put out the window, and low and behold, the wifi network resumed it's usual operation, no weird pings, no users being disconnected, no snail speed connections. It all went back to normal.

There it was, our answer to the greatest mystery of this week. A seemingly innocent smart speaker, connected to the corporate wifi, had somehow crippled our wifi network. It didn't show up on network traffic logs throwing out a load of broadcast traffic, nothing in our other logs suggested it was an issue? 

>Investigations are still ongoing onto how this caused the network issues.

## Future considerations

This should not have happened really. Our wifi network should have been more resilient but it wasn't, and that's something we will improve on. We could debate whether it was the users fault, but in reality, the user shouldn't have been able to connect a device like that to the corporate wifi in the first place.

So some future improvements are:
- Implement more stringent controls for connecting to corp wifi.
- Improve network monitoring solutions to better find faults.

Thank you for reading!
