---
layout: post
title: "Previous Blog Posts"
date: 2017-01-19
---

The following posts I wrote during my time at scip AG.

### Research Related

#### Cross-Site Script Inclusion - A Fameless but Widespread Web Vulnerability Class

> Two key components account for finding vulnerabilities of a certain class: awareness of the vulnerability and ease of finding the vulnerability. Cross-Site Script Inclusion (XSSI) vulnerabilities are not mentioned in the de facto standard for public attention – the OWASP Top 10. Additionally there is no publicly available tool to facilitate finding XSSI. The impact reaches from leaking personal information stored, circumvention of token-based protection to complete compromise of accounts. XSSI vulnerabilities are fairly wide spread and the lack of detection increases the risk of each XSSI. In this paper I am going to demonstrate how to find XSSI, exploit XSSI and also how to protect against XSSI exploitation.
[Read more](https://www.scip.ch/en/?labs.20160414) ([read it in German](https://www.scip.ch/?labs.20160414))

#### Misusing TCP Timestamps
> Before any attack is launched an attacker tries to get as much information about the target as possible. It will be done by passive information gathering like Google dorking as well as active information gathering, like port scanning. Information gathering using TCP timestamps is considered active information gathering, since it interacts directly with the systems.
First we will take a closer look at what TCP timestamps are and how they work. Then we will discuss ways how TCP timestamps have been used, to then move on to how networks have changed and how that has introduced new challenges to information gathering. Last we will introduce a not previously published way to utilize TCP timestamps and also how to mitigate these attacks. [Read more](https://www.scip.ch/en/?labs.20150305) ([read it in German](https://www.scip.ch/?labs.20150305))

### Hacking

#### Razor Code - Don't Cut Yourself

> Some of my favorite blog articles are also the shortest – just a single tip or bypass. This article is written with that in mind and describes how I recently managed to achieve remote code execution (RCE) on a patched Windows server using Razor Code.  [Read more](https://www.scip.ch/en/?labs.20170105) ([read it in German](https://www.scip.ch/?labs.20170105))

#### Metadata Revisited
> Metadata describes data. It has popped up in the news particularly in respect to the discussion of surveillance in general and of phones. Metadata describes in this case, when a call was place, who called whom, the duration of the call et cetera. A lowbrow politician might call this information not sensitive, because the actual phone call is not recorded.
> Metadata are everywhere. Current file systems, so-called Journaling File Systems save data, e.g. the whereabouts on the disc and size of stored data. Images contain metadata about the size and date of creation. If a photo is taken from a mobile phone, it is likely to contain the location where it was taken. Documents often include author and name of the document inside metadata. [Read more](https://www.scip.ch/en/?labs.20150820) ([read it in German](https://www.scip.ch/?labs.20150820))

### Community

#### XSS is Still My Name - A Call for Greater Appreciation of Developers
> The world is fascinated by hackers. Green font on a black background is as much a stereotypical signifier as the hoodie. It’s fascinating because it seems so incomprehensibly complicated and inaccessible. It’s fascinating because it has a hint of the forbidden. That’s as far as most people go. But some are so fascinated that they dig further into the issue and to their enormous surprise discover that it really doesn’t seem that difficult after all. A few months later comes the first mockery of developers and on Twitter they’re saying how simple offensive security is. But nothing is simple – not when you want to take things seriously and do it well. Offensive security is no exception.

> Every specialist area has its hot topics. In IT security, it’s web application security. Web application security is concerned with the security of applications that can be controlled through a browser. As the term hot topic indicates, this was not always the case. Binary exploitation had its time in the sun. For a long time, network security was big news. But then the range of applications expanded and new methods of attack, such as SQL injection, were discovered. Web application security has been the hot topic ever since. [Read more](https://www.scip.ch/en/?labs.20161110) ([read it in German](https://www.scip.ch/?labs.20161110))

### Security Related HTTP Response Headers

#### Big Brother or How I Stopped Worrying and Love Encryption
> Time and time again, surveillance happens. Time and time again, encryption is under attack. And then there’s a debacle with certificates after the next.

> But just because encryption as well as its surrounding systems are under attack does not mean that the solution to this ongoing issue is as difficult as it seems – encryption and correct configuration is all that’s needed. The main challenge is missing knowledge. Knowledge of what to do in order to not fall into one of the many configuration traps. To mitigate that, there are several websites that attempt to spread the knowledge of proper encryption. I am not going into the details as to why encryption is important because whoever reads this is probably past that point already. [Read more](https://www.scip.ch/en/?labs.20151217) ([read it in German](https://www.scip.ch/?labs.20151217))


#### Inglorious Headers
> Protection doesn’t have to be cost much. Neither time nor money. For individuals, I’ve documented protection in the series Healthy Paranoia but there are also tricks for server administrators that grant significantly more security with not that much effort. This article is primarily about headers that are sent with an HTTP response and that influence browser behaviour that aims to prevent Cross-Site-Scripting (XSS) attacks among others.

> During XSS attacks, JavaScript-Code is being snuck into websites. There are several classes of XSS. So-called Reflected XSS that return the user input without any filters in the response. It’s one of the most commonly found XSS vulnerabilities. Stored or Persistent XSS additionally stores the entered code on the website in things such as guest books or forums. DOM-based XSS that uses active code such as JavaScript on the browser side in an insecure way and Mutation-Based XSS that sees browsers interpret slightly altered code and transform it into working code. Other, more obscure versions of XSS are not being respected here. [Read more](https://www.scip.ch/en/?labs.20160121) ([read it in German](https://www.scip.ch/?labs.20160121))

