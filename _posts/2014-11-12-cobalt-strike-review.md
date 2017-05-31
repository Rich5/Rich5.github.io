---
id: 228
title: Cobalt Strike Review
date: 2014-11-12T11:56:04+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=228
permalink: /2014/11/cobalt-strike-review/
categories:
  - Reviews
---
&nbsp;

On a recent engagement I was lucky enough to be able to use Cobalt Strike on a sustained multi-week operation, and overall I was impressed with it&#8217;s performance. This particular engagement was all about adversary emulation, and as far as I&#8217;m concerned Cobalt Strike is the premier tool for that type of job. While it&#8217;s fresh in my mind I just wanted to highlight some of Cobalt Strike&#8217;s features that made life easier on this particular assignment.

For those that have never heard of Cobalt Strike I highly suggest heading over to the [official website](http://www.cobaltstrike.com/) and spending some time reading through all the information there. Cobalt Strike creator, Raphael Mudge, has compiled a blog with a wealth of information and I&#8217;m certainly not going to attempt to revisit everything here.  From the official website, &#8220;Cobalt Strike is penetration testing software that executes targeted attacks and replicates advanced threats.&#8221; If you&#8217;re familiar with Armitage then at first glance you should recognize the Cobalt Strike interface, but there is a world of difference between the two products. In addition to providing a clean graphical interface to the Metasploit Framework, Cobalt Strike comes with it&#8217;s own advanced  features including it&#8217;s own post exploitation tool called Beacon. Beacon was the primary tool I used during this engagement so that&#8217;s what I&#8217;ll be focusing on in this post.

## Listeners

Cobalt Strike is really good at taking care of the routine tasks for you. Setting up listeners is as simple as a few clicks and then specifying a name, payload type, IP, and port. The real payoff  comes later when you&#8217;re generating payloads and spawning sessions on various team servers; the way Cobalt Strike manages all this is an amazing time saver. More on that later.<figure id="attachment_239" style="width: 640px" class="wp-caption aligncenter">

[<img class="size-large wp-image-239" src="http://bytesdarkly.com/wp-content/uploads/2014/11/listeners-1024x567.png" alt="Adding and Editing Listeners" width="640" height="354" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/listeners-1024x567.png 1024w, http://bytesdarkly.com/wp-content/uploads/2014/11/listeners-300x166.png 300w, http://bytesdarkly.com/wp-content/uploads/2014/11/listeners.png 1173w" sizes="(max-width: 640px) 100vw, 640px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/listeners.png)<figcaption class="wp-caption-text">Adding and Editing Listeners</figcaption></figure> 

## Beacon Payloads

Beacon is Cobalt Strike&#8217;s asymmetric post exploitation tool, and  for adversary emulation it is **_the_** tool to use. I&#8217;ll go so far as to say that Beacon is the most advanced post exploitation payload commercially available.  You can generate a few different variations of Beacon payloads (e.g. exe, dropper, staged) that use different protocols namely http, https, and DNS. I used all three protocols during this job, and it was great to have so many options available, especially when combined with Cobalt Strike&#8217;s new Malleable C2 the variations on network signatures are endless.

&nbsp;<figure id="attachment_237" style="width: 640px" class="wp-caption aligncenter">

[<img class="size-large wp-image-237" src="http://bytesdarkly.com/wp-content/uploads/2014/11/create_beacon-1024x572.png" alt="Creating Beacon payloads" width="640" height="357" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/create_beacon-1024x572.png 1024w, http://bytesdarkly.com/wp-content/uploads/2014/11/create_beacon-300x167.png 300w, http://bytesdarkly.com/wp-content/uploads/2014/11/create_beacon.png 1171w" sizes="(max-width: 640px) 100vw, 640px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/create_beacon.png)<figcaption class="wp-caption-text">Creating Beacon payloads</figcaption></figure> 

&nbsp;<figure id="attachment_248" style="width: 640px" class="wp-caption aligncenter">

[<img class="size-large wp-image-248" src="http://bytesdarkly.com/wp-content/uploads/2014/11/beacons-e1415804809673-1024x266.png" alt="Beacon Call-back" width="640" height="166" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/beacons-e1415804809673-1024x266.png 1024w, http://bytesdarkly.com/wp-content/uploads/2014/11/beacons-e1415804809673-300x78.png 300w, http://bytesdarkly.com/wp-content/uploads/2014/11/beacons-e1415804809673.png 1169w" sizes="(max-width: 640px) 100vw, 640px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/beacons-e1415804809673.png)<figcaption class="wp-caption-text">Beacon Call-back</figcaption></figure> 

&nbsp;

Once implanted on a target, Beacon is able to covertly execute commands and other advanced payloads. It can tunnel Meterpreter sessions over it&#8217;s existing communication channel, inject into other processes, steal tokens, use golden tickets, and much more. However, the most useful Beacon feature I discovered was it&#8217;s ability to run Powershell scripts from memory. Running Powershell commands through Beacon is as simple as typing, powershell <cmd>. When I wanted to run an entire ps1 script it was as simple as typing, powershell-import <path to script>, and then calling the newly imported function(s). I found that this new ability to run Powershell scripts over-the-wire changed my overall workflow. Some of my first post exploitation tasks soon became running PowerUp, Powerview, and Invoke-Mimikatz all through Beacon.

&nbsp;<figure id="attachment_241" style="width: 633px" class="wp-caption aligncenter">

[<img class="wp-image-241" src="http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp2.png" alt="PowerUp2" width="633" height="279" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp2.png 726w, http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp2-300x132.png 300w" sizes="(max-width: 633px) 100vw, 633px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp2.png)<figcaption class="wp-caption-text">Calling Invoke-AllChecks from PowerUp</figcaption></figure> 

&nbsp;

&nbsp;<figure id="attachment_242" style="width: 621px" class="wp-caption aligncenter">

[<img class="size-full wp-image-242" src="http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp3.png" alt="PowerUp results" width="621" height="328" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp3.png 621w, http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp3-300x158.png 300w" sizes="(max-width: 621px) 100vw, 621px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/PowerUp3.png)<figcaption class="wp-caption-text">PowerUp results</figcaption></figure> 

&nbsp;<figure id="attachment_238" style="width: 683px" class="wp-caption aligncenter">

[<img class=" wp-image-238" src="http://bytesdarkly.com/wp-content/uploads/2014/11/Invoke-Mimikatz.png" alt="Importing Invoke-Mimikatz.ps1 from Powersploit" width="683" height="251" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/Invoke-Mimikatz.png 876w, http://bytesdarkly.com/wp-content/uploads/2014/11/Invoke-Mimikatz-300x110.png 300w" sizes="(max-width: 683px) 100vw, 683px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/Invoke-Mimikatz.png)<figcaption class="wp-caption-text">Importing Invoke-Mimikatz.ps1 from Powersploit</figcaption></figure> 

&nbsp;

One last feature of Beacon I would like to highlight it&#8217;s ability to chain connections over SMB. Chaining is a great way to egress data through a single data channel rather than each Beacon calling out through the firewall, and it gave me granular control of my data flows that I didn&#8217;t easily have before.

&nbsp;<figure id="attachment_234" style="width: 635px" class="wp-caption aligncenter">

[<img class="size-full wp-image-234" src="http://bytesdarkly.com/wp-content/uploads/2014/11/Beacon_link.png" alt="Beacon linking" width="635" height="322" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/Beacon_link.png 635w, http://bytesdarkly.com/wp-content/uploads/2014/11/Beacon_link-300x152.png 300w" sizes="(max-width: 635px) 100vw, 635px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/Beacon_link.png)<figcaption class="wp-caption-text">Beacon link command</figcaption></figure> 

&nbsp;

Much more information about Beacon can be found on the [Cobalt Strike blog.](http://www.advancedpentest.com/help-beacon)

More on using PowerUp and Powerview can be found on [@HarmJ0y](https://twitter.com/harmj0y)&#8216;s [blog here](http://blog.harmj0y.net/)

## 

## Team Servers

Cobalt Strike  supports the ability to connect to team servers to share sessions and other data which was especially useful during this engagement. There were instances where I lost access (whether by accident or on purpose :), and needed to regain a foothold on a target. Sometimes it would turn out that another team member had a Beacon deeply implanted calling back very low-and-slow to maintain a long-term persistence on the same target. Rather than take control of that Beacon I simply had to request that a session be passed over to my team server using the _spawn_ command. From the Beacon interactive console you can type _spawn_, which will prompt you to specify a listener to call back to, and viola a new Beacon is on it&#8217;s way. What&#8217;s great is that you can spawn a session to any listener located on any team server to which you are connected. &#8211; very cool!

&nbsp;

&nbsp;<figure id="attachment_243" style="width: 560px" class="wp-caption aligncenter">

[<img class="wp-image-243" src="http://bytesdarkly.com/wp-content/uploads/2014/11/spawn.png" alt="spawn" width="560" height="406" srcset="http://bytesdarkly.com/wp-content/uploads/2014/11/spawn.png 942w, http://bytesdarkly.com/wp-content/uploads/2014/11/spawn-300x217.png 300w" sizes="(max-width: 560px) 100vw, 560px" />](http://bytesdarkly.com/wp-content/uploads/2014/11/spawn.png)<figcaption class="wp-caption-text">Spawning a new session to a listener</figcaption></figure> 

&nbsp;

## Final Thoughts

I still have a lot to learn about Cobalt Strike&#8217;s features, but overall it was an amazing tool to work with, and I&#8217;m sure I&#8217;ll be using it a lot in the future.  I&#8217;ve been trying to think of any critiques I have, but after much consideration I realized that my only complaints are mostly that of a developer. That is to say I would have designed or layout some things differently, but I think that about pretty much every other piece of software that I didn&#8217;t write :). Go download the 21 day Cobalt Strike trial- it&#8217;s well worth checking out.

&nbsp;

&nbsp;

&nbsp;

&nbsp;