---
id: 173
title: 'SCE: What is the shell shock security bug?'
date: 2014-09-26T10:25:42+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=173
permalink: /2014/09/sce-what-is-the-shell-shock-security-bug/
categories:
  - SCE
---
_&#8220;Security Concepts Explained&#8221;  (formally&#8221;Security for Grandma&#8221; (SFG)) is a semiregular series attempting to explain the technical details of popular security concepts in way that anyone can understand. My target audience is the regular folks out there so if you&#8217;re an experienced security professionals you may what to check out some of my other posts._

<address>
   
</address>

## The Shell Shock Security Bug

The security community is abuzz with a major security hole found in many popular computer systems across the world. In the media you&#8217;ll see it referred to as &#8220;shell shock&#8221; or &#8220;bash bug&#8221;, and if you believe the hype the world might be coming to an end. To be fair this is a serious problem for anyone that uses a device connected to the Internet, and if you&#8217;re still reading my little obscure blog post then that means you are more than a little concerned (or bored).  As usual many articles referring to shell shock assume a lot from their readers, and do not always explain some of the terms used. If you don&#8217;t understand what a &#8220;shell&#8221; is or you&#8217;ve never heard of &#8220;bash&#8221; then you&#8217;ll probably just skip over most articles &#8211; so lets clear up a few terms.

### What is a shell?

When used in reference to a computer shell then what we&#8217;re talking about is the user interface. Unless someone has printed this post out and given it to you, you&#8217;re probably using some sort of shell to read it. However, not all shells are created equal. Some shells are Graphical User Interfaces (GUI) like most people are used to where you can use the mouse to click items thus commanding the computer to do something. On the other end of the spectrum are Command-line Interfaces (CLI) where you have to type out commands to get the computer to do something. I&#8217;m sure most of you have seen a command-line before, but for the sake of completion a very simple (and unrealistic) example is if you wanted type out a letter you might have to type the command, &#8220;open my letter&#8221; to open a word processor rather than simply clicking an image of a letter on a GUI. In short, a shell is just a way to tell the computer what to do and that&#8217;s it really.

&nbsp;<figure id="attachment_178" style="width: 402px" class="wp-caption alignnone">

[<img class=" wp-image-178" src="/images/2014/09/winxppro.png" alt="Windows Graphical User Interface is an example of a shell" width="402" height="301" srcset="/images/2014/09/winxppro.png 800w, /images/2014/09/winxppro-300x225.png 300w" sizes="(max-width: 402px) 100vw, 402px" />](/images/2014/09/winxppro.png)<figcaption class="wp-caption-text">Windows Graphical User Interface is an example of a shell</figcaption></figure> 

&nbsp;<figure id="attachment_179" style="width: 407px" class="wp-caption alignnone">

[<img class="wp-image-179" src="/images/2014/09/xp_cli.png" alt="xp_cli" width="407" height="206" srcset="/images/2014/09/xp_cli.png 675w, /images/2014/09/xp_cli-300x152.png 300w" sizes="(max-width: 407px) 100vw, 407px" />](/images/2014/09/xp_cli.png)<figcaption class="wp-caption-text">Windows command-line interface (CLI) is also an example of a shell</figcaption></figure> 

&nbsp;

### What is bash?

You will also see &#8220;shell shock&#8221; referred to as the &#8220;bash bug&#8221;, and that&#8217;s because &#8220;bash&#8221; is the name of the shell where the vulnerability was found. Don&#8217;t get too hung up on the name though. It looks similar to the Windows command-line interface shown above, but usually found on computers running Linux and Unix operating systems.  If you&#8217;ve only ever used a Windows computer with the familiar &#8220;Start button&#8221; in the bottom left corner then this may be a mystery to you, but in reality most of the Internet runs on computers that are not like the one you&#8217;re used to seeing. However, if you are a Apple Mac user running OS X then bash is on your computer.<figure id="attachment_186" style="width: 402px" class="wp-caption alignnone">

[<img class=" wp-image-186" src="/images/2014/09/linux_cli.png" alt="Linux command-line shell" width="402" height="270" srcset="/images/2014/09/linux_cli.png 734w, /images/2014/09/linux_cli-300x201.png 300w" sizes="(max-width: 402px) 100vw, 402px" />](/images/2014/09/linux_cli.png)<figcaption class="wp-caption-text">Bash command-line shell</figcaption></figure> 

&nbsp;

### I only have a Windows computer. Why should I care about all this?

Good question. The reason the average user should care is that the shell shock bug can be used to do evil things to websites that you visit. So even though you may only have a Windows computer at home, if you browse the Web then this bug could affect you. For example, if the bad guys can use shell shock to take control of your favorite news website then they can potentially break into your computer the next time you check the headlines. Don&#8217;t go throwing your computer away just yet though. Now that the problem is known IT professionals are quickly trying to plug the hole.

### How does shell shock work?

Unfortunately, this bug is relatively easy to exploit, and there are already reports of bad guys using it to break into computers across the world. What shell shock allows attackers to do is take control of a computer by sending commands to it&#8217;s shell. They are able to do this because certain websites send commands to the shell to be able to function correctly. In a perfect world these commands would be filtered and checked first, but that doesn&#8217;t always happen. So what happens when a bad guy adds another &#8220;evil&#8221; command to a normal command?  Using our above example instead of typing, &#8220;open my letter&#8221;, the bad guys can type, &#8220;open my letter&#8221; and &#8220;delete all files&#8221;. In this case the intended action was to just open a letter, but instead of stopping there and ignoring the rest of the line, as intended, the command &#8220;delete all files&#8221; was also run.

###  Time to panic?

As an average user there is no need to panic. For Mac and Linux users, make sure you update your computer to apply the latest fixes (if available). For Windows users, there is nothing to do for a change. You can rest assured that major companies are working hard to fix any of their systems that are affected; however, many other companies may not be as good about security so in the future I expect to see many stories of security breaches happening due to shell shock. As always been careful with the websites that you visit. If you&#8217;re really concerned then you can stick to major sites only for a while, but there&#8217;s no guarantee that any of us will be protected from the effects of this bug.