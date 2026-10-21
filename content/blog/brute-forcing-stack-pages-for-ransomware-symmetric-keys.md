+++
date = '2026-10-19T22:09:02-03:00'
draft = false
title = 'Brute-forcing stack pages for ransomware symmetric keys'
tags = ['ransomware', 'cryptography', 'volatile memory']
+++

When I started working on my bachelor's thesis during my second-to-last year at university, I wanted to write about malware. I was interested in malware development and wanted to learn more about how hackers implement exploits. What I ended up doing was not really about malware, but about cryptography and volatile memory analysis. 

Kind of a dumb application, at the time I felt like investigating it. In a really convoluted way, it made me want to learn more about cryptography.

My advisor proposed the theme of *ransomware*. At the same time, I had watched some videos about memory analysis (particularly about the Volatility python framework), and thought it was pretty cool. So I decided to combine both themes on a whim and see what I could do.

There's a lot of literature on applying memory analysis to ransomware attacks, surprisingly. The expected application would be for incident response -- as soon as you detect malicious activity, you stop the machine, dump the memory, and try to figure out what happened there. You can analyze the malicious process and find out how it works. If you're lucky, you can get the contents of some file that was being encrypted. But there's not much else you can do for recovery, though. Not anything practical.

You could try to find, in memory, the encryption key being used by the ransomware. If you get the key, you can hopefully recover your files -- but only if you're dealing with a really dumb baby ransomware. 

Most sophisticated ransomware don't use the same key for every file. They use hybrid encryption. It consists of generating a different symmetric key for each file, and encrypting it with a public key using asymmetric cryptography. The encrypted key is stored with the encrypted file content, and since only the attacker has the corresponding private key, there's no way to recover it.

As an experiment, I set a random ransomware free on a Windows virtual machine, and captured the memory while it was encrypting a really big text file (this was pretty hard to do using an old laptop with 4gb of RAM). The objective was to find out if I can find the key and decrypt the file, and what's the best way to do it. 

The result is, of course, that you can definitely find it, if you look hard enough. 

Since encryption keys are random, and code and other types of data are mostly structured, you can test if a region of memory contains a possible key by calculating its entropy. There are also ways to reduce the amount of memory you need to search through: using Volatility, I isolated the memory of the malicious process, and selected the individual memory pages that most likely contained data from the stack, where the program stores its local variables. Once you have a list of possible keys, you just need to test decryption on every single one until you find something that looks like your file.

Despite the obvious limitations, it's interesting to learn about finding encryption keys in memory. There are real applications too! For example, breaking disk encryption. There's not much you can't do when you have access to the memory.

There's a lot of details about how ransomware works, types of encryption, and how to find data in memory that I had the opportunity to learn. I also wrote a paper on it and presented it at a cyber security event in Brazil. You can find it at https://sol.sbc.org.br/index.php/sbseg/article/view/30025