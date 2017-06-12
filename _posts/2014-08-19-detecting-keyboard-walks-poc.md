---
id: 81
title: 'Detecting Keyboard Walks: POC'
date: 2014-08-19T10:02:55+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=81
permalink: /2014/08/detecting-keyboard-walks-poc/
categories:
  - Password Cracking
  - Python
---
&nbsp;

<h4 style="padding-left: 60px;">
  Quick Links:
</h4>

<p style="padding-left: 60px;">
  Repo: <a href="https://github.com/Rich5/Keyboard-Walk-Generators/tree/master/Analysis%20Tools">https://github.com/Rich5/Keyboard-Walk-Generators/tree/master/Analysis%20Tools</a>
</p>

&nbsp;

At this point it feels a little like beating a dead horse, but I wanted to follow-up my previous post with an idea that occurred to me this weekend. I&#8217;ve been told that you shouldn&#8217;t pose a problem with at least outlining a few possible solutions, and I think this is a good rule for information security too. So with that in mind I wanted to show how you can use the tree walk data structure I previously presented to detect if users are trying to set a keyboard walk as a password. I&#8217;d like to point out that this technique is only presented as a proof of concept, but the methodology could probably be implemented on a domain wide scale to set a password policy preventing keyboard walks. I&#8217;m sure there are  probably other ways to enforce this as well.

I&#8217;ve seen a wide range of password policies, and some of them are rather Draconian. For example, one system I worked on required a password that didn&#8217;t use any adjacent letters. This policy had the effect of annoying the users mostly, and still didn&#8217;t prevent all keyboard walks (e.g. z1x2c3v4). I&#8217;ve added a script [here](https://github.com/Rich5/Keyboard-Walk-Generators/tree/master/Analysis%20Tools) that will use the [tree data structure](https://github.com/Rich5/Keyboard-Walk-Generators/blob/master/Method%201%20-%20Tree%20Walks/qwerty_graph.txt) to detect if a keyboard walk is present in a string. This script isn&#8217;t perfect and detects strings that are walks in a technical sense, but maybe not exactly what you&#8217;re used to seeing. For example, running this script on the rockyou word list gives some interesting results. Here&#8217;s some sample output:

<pre style="padding-left: 30px;"><code>
...

123456
654321
qwerty
121212
987654
456789
asdfgh
232323
212121
zxcvbn
098765
1q2w3e
234567
090909
454545
898989
565656
redred
qwaszx
567890
lololo
909090

...
</code></pre>

You&#8217;ll notice that obvious walks like, _qwerty_ and _123456_ are detected, but also strings like 232323 which some may not consider a valid result.  I would say that _232323_ is a true positive, but what constitutes a keyboard walk is all in the eyes of the cracker I guess (i.e _redred_  is questionable).  Using the WalkCheck.py script I came up with a quick analysis of the rockyou word list.

Passwords containing walks of x length/total possible:

<center>
<table>
  <tr>
    <td>
      L
    </td>
    
    <td>
      Total Walks
    </td>
    
    <td>
      Total Possible*
    </td>
    
    <td>
      Percentage
    </td>
  </tr>
  
  <tr>
    <td>
      4
    </td>
    
    <td>
      201345
    </td>
    
    <td>
      14344005
    </td>
    
    <td>
      1.4
    </td>
  </tr>
  
  <tr>
    <td>
      5
    </td>
    
    <td>
      72048
    </td>
    
    <td>
      14341544
    </td>
    
    <td>
      .5
    </td>
  </tr>
  
  <tr>
    <td>
      6
    </td>
    
    <td>
      34579
    </td>
    
    <td>
      14323645
    </td>
    
    <td>
      .2
    </td>
  </tr>
  
  <tr>
    <td>
      7
    </td>
    
    <td>
      16260
    </td>
    
    <td>
      14064476
    </td>
    
    <td>
      .1
    </td>
  </tr>
  
  <tr>
    <td>
      8
    </td>
    
    <td>
      10904
    </td>
    
    <td>
      12116679
    </td>
    
    <td>
      .1
    </td>
  </tr>
</table>
</center>
*Total possible means candidates of at least length L

&nbsp;

&nbsp;