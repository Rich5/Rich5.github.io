---
id: 350
title: 'Harness: Intro and Usage Guide'
date: 2015-10-04T13:28:10+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=350
permalink: /2015/10/harness-intro-and-usage-guide/
categories:
  - PowerShell
---
# Intro

At DefCon 23 I released a beta version of a project I was working on called [Harness](https://github.com/Rich5/Harness). Harness is remote access payload with the ability to provide a remote interactive PowerShell interface from a Windows system to virtually any TCP socket. The primary goal of the Harness Project is to provide a remote interface with the same capabilities and overall feel of the native PowerShell executable bundled with the Windows. At the time of release I did not have a good usage guide available so this article is intended to serve as an introduction and basic usage guide.

As of 25 September 2015 Harness 1.0 is available for download with added features and bug fixes.  I’ve been slowly adding much of this information to the github wiki where the project source code is located.

# Goals

When I started the project I had two goals in mind:

  1. I wanted a fully interactive remote PowerShell console with the same capabilities as the native PowerShell.exe
  2. The ability to seamlessly import modules across the wire

# Architecture

## Server

Of course Harness follows the client-server model common across the Internet. For the purposes of this guide, and clarity, the Harness payload is the server and any TCP listener can be the client. For example, Harness will easily work with netcat; however, some of the more advanced features (e.g. ^import-module) built into Harness requires a companion handler that supports the built-in commands.

## Native Client

The native Harness handler is contained within the _PSHandler_ module. The _PSHandler_ module implements the special command ^import-module that gives Harness the ability to import modules across the wire without staging PowerShell scripts on a web server. _PSHandler_ is currently an experimental handler utilizing the Python asyncio library included in the Python 3.4 and above; however using asyncio is **not** necessarily required and implementing the ^import-module command in other handlers should be straight forward. See [Development](https://github.com/Rich5/Harness/wiki/Development) section of the wiki for more implementation details.

# Installation

## Installing Python 3.4

Many developers have not moved to Python 3.x, and believe me I get it. There were some major changes in Python 3, and people are resistant to change. So we continue to see more and more tools being pushed out written in Python 2.7. There’s nothing wrong with this in practice although new features will not be available for Python 2 by default. I wanted to limit the use of external libraries that users would have to install, so instead I opted to develop the framework in Python 3.4 to take advantage of new standard libraries like asyncio. Yes, I could have accomplished a solution in Python 2 also, but I didn’t. Luckily, it’s really easy to run Python 2 and Python 3 side-by-side. I’ve included a simple bash script to quickly download, build, and install Python 3.4 as an alternate install. To download and install Harness you can run the following commands on your Linux system:

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> git clone https://github.com/Rich5/Harness.git  
 cd Harness/  
 chmod 755 python_install.sh  
 ./python_install.sh  
</code></pre>

python_install.sh will run the following commands to download and install Python 3.4.

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> wget http://python.org/ftp/python/3.4.3/Python-3.4.3.tar.xz  
 tar xf Python-3.4.3.tar.xz  
 cd Python-3.4.3  
 ./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"  
 make && make altinstall  
</code></pre>

Once installed start the framework by running:

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> Python3.4 harness.py  
</code></pre>

&nbsp;

[<img class="aligncenter size-full wp-image-358" src="/images/2015/10/harness_main.png" alt="harness_main" width="533" height="424" srcset="/images/2015/10/harness_main.png 533w, /images/2015/10/harness_main-300x239.png 300w" sizes="(max-width: 533px) 100vw, 533px" />](/images/2015/10/harness_main.png)

&nbsp;

# Client Framework

Harness is bundled in a small modular framework written in Python; however, the intent is for users to ultimately incorporate the server into their own toolsets if useful. With so many tools, frameworks, etc. there are far more refined security testing frameworks available.  Typing **_show modules_** will display the available modules:

[<img class="aligncenter size-full wp-image-362" src="/images/2015/10/harness_showmodules.png" alt="harness_showmodules" width="722" height="334" srcset="/images/2015/10/harness_showmodules.png 722w, /images/2015/10/harness_showmodules-300x139.png 300w" sizes="(max-width: 722px) 100vw, 722px" />](/images/2015/10/harness_showmodules.png)

&nbsp;

# Payloads

Generating payloads is fairly straight forward. To load a module type **_load <module name>_** to enter into the module sub-prompt. Then using the familiar **_show_** and **_set_** commands you can configure the payload. All modules are executed by typing **run** at the prompt.  Payload modules will then ask you to specify a location to save the file.

[<img class="aligncenter size-full wp-image-357" src="/images/2015/10/harness_load_module_x86exe.png" alt="harness_load_module_x86exe" width="724" height="388" srcset="/images/2015/10/harness_load_module_x86exe.png 724w, /images/2015/10/harness_load_module_x86exe-300x161.png 300w" sizes="(max-width: 724px) 100vw, 724px" />](/images/2015/10/harness_load_module_x86exe.png)

It’s up to the user to determine how to get the payload on the target. Executable payloads may be dropped to disk, and reflective payloads can be injected into a process using various techniques including <a href="https://www.rapid7.com/db/modules/post/windows/manage/reflective_dll_inject" target="_blank">Metasploit&#8217;s reflective dll inject module</a>. In the future delivery modules will be included to take advantage of the usual attack vectors (staged payload, macro, PowerShell download, etc).

&nbsp;

# Starting the Listener

Loading the native handler is similar to any other module. Simply set the IP and PORT you want to listen on and type run.

[<img class="aligncenter size-large wp-image-360" src="/images/2015/10/harness_run_handler-1024x384.png" alt="harness_run_handler" width="700" height="263" srcset="/images/2015/10/harness_run_handler-1024x384.png 1024w, /images/2015/10/harness_run_handler-300x113.png 300w, /images/2015/10/harness_run_handler.png 1104w" sizes="(max-width: 700px) 100vw, 700px" />](/images/2015/10/harness_run_handler.png)

As you can see the handler module is forced to run the background by design, and you now have a listener running on port 80. Multiple connections can be handled by the same handler. By default the handler is listening for SSL connections. This is not required, but should you want to use SSL you’ll need to generate your own self-signed certificates, and set the CERT\_PATH and KEY\_PATH variables accordingly.

On Linux a simple way to generate a self-signed certificate is with the following line:

<pre style="font-family: arial; font-size: 12px; border: 1px dashed #CCCCCC; width: 99%; height: auto; overflow: auto; background: #f0f0f0; padding: 0px; color: #000000; text-align: left; line-height: 20px;"><code style="color: #000000; word-wrap: normal;"> openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.cert  
</code></pre>

# Shell

Currently the payload is a reverse shell, although there’s code for a bind shell already implemented in the source, and could easily be used if someone is motivated enough. Once the payload is executed a session is registered with the framework and is available with the session command.

[<img class="aligncenter size-full wp-image-355" src="/images/2015/10/harness_gaining_shell.png" alt="harness_gaining_shell" width="923" height="380" srcset="/images/2015/10/harness_gaining_shell.png 923w, /images/2015/10/harness_gaining_shell-300x124.png 300w" sizes="(max-width: 923px) 100vw, 923px" />](/images/2015/10/harness_gaining_shell.png)

Typing **_session <session number>_** will present you with the familiar PowerShell prompt. Harness is a completely custom host implemented in C# and C++. That means that all the features of the native PowerShell.exe had to be implemented by hand. Most of the host methods have been implemented in some capacity, but not all, and certainly not as well as the Microsoft developers did with the native executable. What this means is that most users will not notice much difference in their experience, but some of the more complex scripts may not function as expected. It’s impossible to test all the scripts that any given user may try. So if you come across a bug or strange behavior please submit a bug report on Github. As with all things I recommend testing thoroughly in the lab before trying out a technique on a target system.

# Special Commands

Harness is intended to allow the user to utilize their own PowerShell scripts as a custom toolkit, but there some special commands that give Harness additional functionality. Commands prefixed with the ^ character indicate that special handling is required. The special commands can be handled on the client side, server side, or both. For example, the **_^import-module _**function requires handling on both the client and server while the **_^enable-format_** command is only handled on the server side.

## ^Import-Module

Allows the user to seamlessly import any PowerShell module remotely without requiring staging.

### Usage: ^import-module <path to ps1 file>

[<img class="aligncenter size-full wp-image-356" src="/images/2015/10/harness_import_module.png" alt="harness_import_module" width="1014" height="156" srcset="/images/2015/10/harness_import_module.png 1014w, /images/2015/10/harness_import_module-300x46.png 300w" sizes="(max-width: 1014px) 100vw, 1014px" />](/images/2015/10/harness_import_module.png)

The framework will take care of base64 encoding the file and sending it across the wire. In the event there is file corruption, during transfer, the Harness payload will usually alert you that there is a parse error and to try again. Once a module has been imported you may call the functions as usual. The example below show calling _I**nvoke-AllChecks**_ from <a href="https://github.com/PowerShellEmpire/PowerTools/tree/master/PowerUp" target="_blank">PowerUp</a>.

[<img class="aligncenter size-full wp-image-361" src="/images/2015/10/harness_run_PowerUp.png" alt="harness_run_PowerUp" width="658" height="310" srcset="/images/2015/10/harness_run_PowerUp.png 658w, /images/2015/10/harness_run_PowerUp-300x141.png 300w" sizes="(max-width: 658px) 100vw, 658px" />](/images/2015/10/harness_run_PowerUp.png)

## ^End and Multiline Input

One of the nice features of the native PowerShell.exe in that users can enter multi-line input. This makes it easy to construct simple functions or more complex command sequences on the fly. Harness implements multi-line input using a simple accumulator loop in conjunction with a PowerShell syntax parser. Every command sent to the server is checked for syntax errors. If parse errors are found the server sets a flag, changes the prompt (e.g. >>) to indicate multi-line input, and starts accumulating input until the script is complete. To break out of multi-line input use the **_^end_** special command rather than attempt CTRL-C.

### Usage: ^end

[<img class="aligncenter size-full wp-image-359" src="/images/2015/10/harness_multiline_input.png" alt="harness_multiline_input" width="702" height="292" srcset="/images/2015/10/harness_multiline_input.png 702w, /images/2015/10/harness_multiline_input-300x125.png 300w" sizes="(max-width: 702px) 100vw, 702px" />](/images/2015/10/harness_multiline_input.png)

In the example below I have a typo that caused a parse error. Since I’m not able to complete the script the accumulator will never return out of the sub-prompt loop. Rather than kill the shell the **_^end_** command allows you to break out of the loop cleanly without terminating the shell.

[<img class="aligncenter size-full wp-image-353" src="/images/2015/10/harness_end_command.png" alt="harness_end_command" width="846" height="135" srcset="/images/2015/10/harness_end_command.png 846w, /images/2015/10/harness_end_command-300x48.png 300w" sizes="(max-width: 846px) 100vw, 846px" />](/images/2015/10/harness_end_command.png)

## ^sleep

The **_^sleep_** command allows the user to suspend the shell and schedule a call back at a specified time.  You may use a specific time or simply enter an offset from the current time. _ NOTE: time is relative to the time on the target machine. This will be simplified by normalizing all time to UTC in future releases._

### Usage: ^sleep [+Xd|h|m|s] | [dd/mm/yyyy hh:mm ]

[<img class="aligncenter size-full wp-image-363" src="/images/2015/10/harness_sleep.png" alt="harness_sleep" width="947" height="255" srcset="/images/2015/10/harness_sleep.png 947w, /images/2015/10/harness_sleep-300x81.png 300w" sizes="(max-width: 947px) 100vw, 947px" />](/images/2015/10/harness_sleep.png)

&nbsp;

## ^enable-format and ^disable-format

All commands sent to the Harness payload are piped to the Out-String cmdlet to format output. If for some reason you do not want this behavior you can disable this feature with the **_^disable-format_** command. Likewise **_^enable-format_** should be self-explanatory.

### Usage: ^enable-format

###                ^disable-format

[<img class="aligncenter size-full wp-image-354" src="/images/2015/10/harness_formating.png" alt="harness_formating" width="983" height="472" srcset="/images/2015/10/harness_formating.png 983w, /images/2015/10/harness_formating-300x144.png 300w" sizes="(max-width: 983px) 100vw, 983px" />](/images/2015/10/harness_formating.png)

&nbsp;

# Conclusion

Overall Harness is a fun project, and I learned a lot things along the way. I&#8217;ll be adding updates and features as time allows. This is just one more tool to add to your toolbox. Use what works best for the situation at hand.

Download Harness here: <https://github.com/Rich5/Harness>

&nbsp;