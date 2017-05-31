---
id: 107
title: 'SCE: Security concepts from the Celebrity iCloud hack'
date: 2014-09-05T13:23:28+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=107
permalink: /2014/09/sce-security-concepts-from-the-celebrity-icloud-hack/
categories:
  - SCE
---
### _Security Concepts Explained_

_I have decided to start a routine blog post related to explaining security principals in a way that, hopefully, everyone should be able to understand. I&#8217;m calling these posts, Security For Grandma (SFG), because as I write these posts I&#8217;m going to try to explain topics in the same way that I would if my grandmother asked me to explain some &#8220;hacking thing&#8221; that showed up on the news. If you&#8217;re a seasoned security professional you&#8217;re welcome to continue reading of course, but please don&#8217;t be surprised by the simplicity of the topics._

_Update: SFG has been renamed to Security Concepts Explained for clarity. The idea is the same._

&nbsp;

## Celebrity iCloud Hack

In my first SFG post I&#8217;ll be talking about the compromise of hundreds of celebrity iCloud accounts resulting in the leak of many personal, and mostly embarrassing,  photographs of some of our favorite stars. At this point I think most people are familiar with the headlines, but it you don&#8217;t know what I&#8217;m talking about take a minute to [Google &#8220;Celebrity Hacking&#8221;](https://www.google.com/search?sourceid=chrome-psyapi2&ion=1&espv=&ie=UTF-8&q=celebrity%20hacking) and read a few news posts. Now, let&#8217;s address some terms that the media throws around without really explaining much of what they&#8217;re talking about.

#### _iCloud_

What is this iCloud you speak of? While we&#8217;re on the subject what is this whole &#8220;cloud&#8221; thing anyway? In the most simple terms the &#8220;cloud&#8221;, these days, refers to a computer or collection of computers connected to the Internet where you store information. It&#8217;s a bit more complicated, but for the general public all you need to know is that it&#8217;s just another computer somewhere where your data is being kept so you can access it anywhere there is Internet access. For example, you&#8217;re writing a story on your home computer, and you want to read it on your phone later. So you save your story to a computer in the &#8220;cloud&#8221;, and then use your phone to retrieve it from the the remote computer you stored it on, and read it later at the airport.

Why is it called the &#8220;cloud&#8221;? Mostly out of laziness. In network engineering when you draw a diagram of how computers and systems are connected eventually you want to show how they&#8217;re connected to the broader Internet. Rather than worry about the details of the millions of systems on the Internet we just draw a big cloud and label it &#8220;Internet&#8221; or &#8220;World Wide Web&#8221;. Think about it this way, if you were drawing a map of your neighborhood, and you get to the point where your neighborhood connects to a major city; rather than draw every street in New York City, for example, you can just draw a big cloud picture and label it NYC &#8211; the details are unimportant in relation to your neighborhood map. Unfortunately, a simple icon used by engineers has become a marketing buzz word that is nebulous at best.

So the bottom line is the iCloud is just a bunch of computers owned by Apple where their customers can keep their files, music, and any picture that might be on their phone. iCloud a brand name; that&#8217;s all.

#### _Brute-force Attack_

When you hear the phrase brute-force attack this is just a fancy way of saying they guessed some piece of information. In this case the attackers may have simply guessed the celebrities&#8217; passwords.  For example, let&#8217;s say we were trying to get into &#8220;John&#8217;s&#8221; account on Apple&#8217;s iCloud. In this case, we could &#8220;brute-force&#8221; our way into John&#8217;s account by going to the iCloud login screen and try different passwords until one worked. In practice human&#8217;s rarely do this by hand instead we use computer programs to try out thousands of passwords for us.

#### _Two-step Authentication_

Sometimes you&#8217;ll see this called two-factor authentication or multi-factor authentication, but it&#8217;s referring to the same concept. The easiest way to describe two-factor authentication is to use an example. Let&#8217;s say you want to get into a secret building so you go to the door and knock. A man opens the door, and asks for the secret password. You tell him the correct password, but then he asks you to show him your &#8220;secret building&#8221; badge. Since you don&#8217;t have a badge the man doesn&#8217;t let you into the building. In this case you needed two things to get into the building &#8211; a secret password and a badge. Many online services like email or cloud storage now offer two-factor authentication as an option. For example, you can setup your gmail account to require that you enter both a password and a separate one-time use code that is sent to your phone. So you need to know your password, and have access to your phone in order to login to your email account. In other words, you need two-factors, something you know (password) and something you have (phone).

### My Take on the Attack

Now that the vocabulary is out of the way we can talk about the celebrity iCloud accounts that were hacked. Everyone is asking the same question- how did this happen? The short answer is that we don&#8217;t know yet. One theory is that attackers brute-forced the celebrity iCloud accounts. If this is true then both Apple and the celebrities are to blame, and here&#8217;s why. It is common practice to limit the amount of times you can attempt and fail to log into an account. Once you exceed the maximum login attempts many websites will freeze your account and/or not allow you to try to login for a set amount of time (e.g. 5 mins).  If Apple did not limit login attempts then they failed at security on a very basic level allowing attackers to guess at passwords as many times as they want. However, the users of the iCloud accounts are also to blame in this case. I&#8217;m not going to discuss the wisdom of taking nude photos and trusting a third party (in this case Apple) to protect them, but a good rule is that you should pick strong passwords that have no connection to your life. If your dog&#8217;s name is &#8220;spot&#8221;, then _spot123_ is not a good password. When guessing passwords it&#8217;s standard practice to make educated guesses by gathering information on your target, and there is A LOT of information readily available out there about celebrities. Another possible cause for the breach is that there is a problem with Apple&#8217;s iCloud. _**Apple has completely denied this, and so far I have seen no evidence that this is the problem. **_This scenario is far more scary because it means that there is very little users can do to help protect their data. In this case it&#8217;s up to Apple to fix the hole and plug it before more information is leaked.

So what can we do as users? The media keeps posting the following recommendations:

1. Pick strong passwords that are at least 12 characters with a mix of numbers, upper and lower letters, and special characters (e.g !@#$%^&*())

2. Use two-factor authentication (instructions vary by account)

These are good suggestions, but I&#8217;d like to add this one. Don&#8217;t use the verification questions to reset your password. On many sites if you forget your password you can fill in pre-answered questions to reset your password. Most verification questions are easily answered by looking up public records. For example, using a complex password like, qserqw34!@#$QWE$!@#$, does nothing to protect you if all I need to know is your mother&#8217;s maiden name to reset your password to whatever I want_. _

My final point is this &#8211; when you use the &#8220;cloud&#8221; you&#8217;re trusting someone else with your data. It&#8217;s no different than keeping your money in a bank rather than under your mattress. Protect your passwords like you would your ATM PIN. Choose who you trust with your personal information wisely, and be careful with what information you share. As a general rule just assume that all information on the cloud could one day become public knowledge.

&nbsp;

&nbsp;