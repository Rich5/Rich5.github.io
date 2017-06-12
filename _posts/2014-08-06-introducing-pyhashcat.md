---
id: 7
title: Introducing pyHashcat
date: 2014-08-06T16:17:07+00:00
author: Rich
layout: post
guid: http://bytesdarkly.com/?p=7
permalink: /2014/08/introducing-pyhashcat/
categories:
  - Password Cracking
  - Python
---

<p>NOTE: This is a cross post from my article on www.sploitmonkey.com></p>

By far my favorite password cracking tool is oclHashcat. It’s extremely fast, easy to use, and provides nearly every feature that I want in a password cracker. If I have GPU cycles available I’ll use oclHashcat otherwise I’ll use hashcat (CPU based) or john. On a recent project I needed to be able to easily manage a lot of captured hashes and try them out against multiple attack methods such as dictionary, hybrid, and markov chains to name a few. oclHashcat supports all of these attack methods (and more!), but what I wanted was a system that would automate my password cracking tasks and managed captured credentials.

My first thought was to just use a collection of bash scripts to feed input into oclHashcat. Many people use this technique and I’m sure it gets the job done, but I wanted something more robust. I wanted a system that I could easily feed input into oclHashcat, and return output in a format of my choosing to be parsed and organized. My general purpose language of choice is Python so I set out writing a password cracking system to my requirements; however, along the way ran into some issues interfacing with oclHashcat. As of this writing oclHashcat does not have an API, to speak of, so in order to interface with the program you have to use standard streams or read and write to the various input/output files available. Using Python to interface with streams is easy enough, but I wanted a simple interface to oclHashcat without worrying about creating command line arguments, creating new processes, or converting output to Python data structures. The solution I came up with is a small Python module that acts as a simple wrapper to oclHashcat.

The pyHashcat 0.02 beta module is [available on GitHub](https://github.com/Rich5/pyHashcat) and is released under the MIT License. The main idea is to make interfacing with oclHashcat as easy as writing Python scripts.

List of features:

  * Interface with oclHashcat as a Python object
  * Run oclHashcat as a background process
  * Automatically builds command line arguments based on Python object variable assignment
  * Support for stdout stream gobbling
  * Support for stderr stream gobbling
  * Parse output from oclHashcat files and return data as a Python dictionary data structure with user defined fields
  * Parse restore files and return data as a Python dictionary data structure
  * Wrapper class has context manager (i.e. with) that will automatically clean-up oclHashcat background process on object termination
  * Look-up function for mapping hash types to oclHashcat type codes

Below are a few examples of what it looks like to use pyHashcat. It runs without output by default, but setting verbose mode will generate input for debugging. It&#8217;s beta right now so if you run into any bugs please let me know.

```
 '''  
      From: cudaExample0.cmd using Python context manager  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
      hashcat.hash_file = "example0.hash"  
      hashcat.markov_threshold = 32  
      hashcat.words_files.append("example.dict")  
      hashcat.mask = "?a?a?a?a"  
      hashcat.hybrid_mask_dict()  
      while hashcat.is_running():  
           print hashcat.stdout()     # Simple Stream gobbler
  
```

&nbsp;

```
'''
From: cudaExample0.cmd without context manager

'''
import pyHashcat&lt
hashcat = pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='ocl', verbose=True)
hashcat.hash_file = "example0.hash"
hashcat.markov_threshold = 32
hashcat.words_files.append("example.dict")
hashcat.mask = "?a?a?a?a"
hashcat.hybrid_mask_dict()
while hashcat.is_running():
print hashcat.stdout()
hashcat.stop()
```
```

 '''  
     From: Hybrid-Dictionary attack  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
     hashcat.hash_file = "example0.hash"  
     hashcat.markov_threshold = 32  
     hashcat.words_files.append("example.dict")  
     hashcat.mask = "?a?a?a?a"  
     hashcat.hybrid_dict_mask()  
     while hashcat.is_running():  
         print hashcat.stdout() # Simple Stream gobbler  

```

&nbsp;

```
 '''  
      Example with rules  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
     hashcat.words_files.append("example.dict")  
     hashcat.rules_files.append("best42.rule")  
     hashcat.rules_files.append("custom.rule")  
     hashcat.hash_type = "NTLM"  
     hashcat.straight()  
     while hashcat.is_running():  
         print hashcat.stdout() # Simple Stream gobbler  

```
&nbsp;

```
 '''  
      Test example  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
     hashcat.words_files.append("example.dict")  
     hashcat.rules_files.append("best42.rule")  
     hashcat.rules_files.append("custom.rule")  
     hashcat.hash_type = "NTLM"  
     hashcat.straight(TEST=True)     # Will print stdin arguments without starting process  

```

&nbsp;

```

 '''  
      Using the start function       
      NOTE: start function does not support the TEST option  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
     hashcat.start(argv=["-m", "1000", "-a", "0", "..\\hashes.hash", "dictionary.dict", "-r", "myrules.rule"])  
     while hashcat.is_running():  
          print hashcat.stdout()  
```

&nbsp;

```

 '''  
      Parse outfile example  
 '''  
 import pyHashcat  
 with pyHashcat.oclHashcatWrapper(path_to_exe, gcard_type='cuda', verbose=True) as hashcat:  
     hashcat.words_files.append("example.dict")  
     hashcat.rules_files.append("best42.rule")  
     hashcat.rules_files.append("custom.rule")  
     hashcat.hash_type = "NTLM"  
     hashcat.outfile = "myoutput.txt"  
     hashcat.straight()  
     while hashcat.is_running():  
           pass  
     print hashcat.get_hashes(fields=('first', 'second', 'third'))  

```