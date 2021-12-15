---
author: "Yiming Shen"
date: 2021-11-09
lastmod: 2021-12-14
title: "Public Key Cryptology"
tags: [
    "cryptology",
    "Public Key",
    "Diffie-Hellman",
]
categories: [
    "English",
]
mathjax: true
typora-root-url: ..\static\
---

## CONTEXT

​		The first general purpose computers were built in the 1940s. They were large, filling big rooms, using hundreds of valves and consuming many kilowatts of electricity. Although they were considered amazing for the ability to execute thousands of instructions per second at that time, all these huge machines were rapidly became history and might have been lost in the mists of time. With the increasing speed of computing, networks of computers were established allowing people to have access to communicate with others in distant places. And, so the network information security became an issue from then on.

## PURPOSE

​		A situation is that X and Y wish to communicate with each other and they want to be sure that their messages shouldn't be intelligible to any other listener. Of course, they need to build a encryption system which they must assume is known to others, requires the use of one or more keys to transmit and decipher their messages. But how can X and Y tell each other their keys without exploiting them.

## METHODS

​		An elegant solution to the key exchange problem was proposed by Diffie and Hellman in 1976. Their method is implemented by X and Y as follows:

1. X and Y uses two integers p, a large prime and m lies between 1 and 	(p-1), both of them are public.

2. X chooses a secret number $x$ and Y chooses $y$. Both $x$ and $y$ lie between 1 and (p-1). X and Y do not reveal $x$ and $y$ to each other and anyone else.

3. X and Y computes the number
   
   
   $$
   k_x = m^x (mod\quad p)\\\\
   k_y = m^y (mod\quad p)
   $$
   
4. Now
   
   
   $$
   (k_x)^y = (k_y)^x \equiv m^{xy}(mod\quad p)=K(say)
   $$
   
   
   K is the common key which both X an Y can use for encryption.

## RESULTS

​		We got a public key for the next encryption, but how secure is the Diffie-Hellman system? The security depends upon how difficult it would be for the third party to obtain the value $x$. This is known to be an extremely difficult problem as 'the discrete logarithm problem'. In general it can be considered impossible unless the prime, p, is of a special form.

## DISCUSSION

​		However, an alternative attack which the third party Z could employ by retaining the  values of $$k_x$$ and $$k_y$$ and substituting a value of his own, then send it to both X and Y.  Z would then re-encrypt the messages using his keys.

​		Despite this potential weakness the Diffie-Hellman system has, it can be used as the starting point for other new systems such as DES and RSA in particular.

