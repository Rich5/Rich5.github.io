---
id: 122
title: pyHashcat v0.5 beta Released
date: 2014-09-12T08:44:19+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=122
permalink: /2014/09/pyhashcat-v0-5-beta-released/
categories:
  - Password Cracking
  - Python
---

pyHashcat v0.5 has been released on github. You can find it [here](https://github.com/Rich5/pyHashcat). If you&#8217;re not familiar with pyHashcat you can read a little about it [here](http://bytesdarkly.com/2014/08/introducing-pyhashcat/).

#### New features:

  * Added support for hashcat via the HashcatWrapper class. Usage is similar to the oclHashcatWrapper class. See the README.txt
  * Support for new hash types added to oclHashcat v1.30
  * The get\_restore\_stats function has been re-written by Michael Sprecher (@hops_ch), and is now more stable
  * Added cpu_type to HashcatWrapper to use AVX/XOP binaries ( again thanks to Michael Sprecher)
  * Other small bug fixes and general code clean-up

Enjoy! And of course let me know if/when bugs show up. It is a beta release after all 🙂

&nbsp;