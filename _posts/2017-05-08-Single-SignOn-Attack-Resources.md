---
layout: post
title: "Single Sign-On Attack Resources"
date: 2017-05-03
---
Attacks on SSO protocols are an active field of research and finding good sources can be a bit of a nuisance. I recently invested time into research, resulting in the following collection of links, papers and tools. The list has been curated and filtered to only show links I consider to be noteworthy or particularly helpful. It is not and not intended to be comprehensive.

![archive]({{ site.url }}/assets/resource01.png)

### Attacks

- Signature Exclusion Attack: Check if the signature is only validated if the signature is included in the request
- XML Signature Wrapping (XSW) Attacks: Put your evil assertion somewhere else
- XML Parser Library Bugs
- Replay Attack: Use the same token again

### Links / Resources

#### Offensive

##### SAML

- Paper: [On Breaking SAML: Be Whoever You Want to Be](https://www.usenix.org/system/files/conference/usenixsecurity12/sec12-final91.pdf)
- Blog post: [Attacking SSO: Common SAML Vulnerabilities and Ways to Find Them](https://blog.netspi.com/attacking-sso-common-saml-vulnerabilities-ways-find/)
- Dissertation by [Juraj Somorovsky](https://www.nds.rub.de/media/nds/veroeffentlichungen/2013/11/19/diss_somorovsky.pdf) 
- Dissertation by [Andreas Mayer](http://www-brs.ub.ruhr-uni-bochum.de/netahtml/HSS/Diss/MayerAndreas/diss.pdf)
- Blog post: [Bypassing SAML 2.0 SSO with XML Signature Attacks](http://research.aurainfosec.io/bypassing-saml20-SSO/)
- Blog post: [The road to your codebase is paved with forged assertions](http://www.economyofmechanism.com/github-saml.html)

##### OpenID Connect

Not to be confused with OpenID.

- Paper: [SoK: Single Sign-On Security - An Evaluation of OpenID Connect](https://www.nds.rub.de/media/ei/veroeffentlichungen/2017/01/30/oidc-security.pdf)

##### Mixed

- Bachelor Thesis: [Automatic Recognition, Processing and Attacking of Signle Sign-On Protocols with Burp Suite](https://www.nds.rub.de/media/nds/arbeiten/2015/10/30/Tim_Guenther-EsPReSSO-BA.pdf)
- Paper and similar but slightly different content:  [Automatic Recognition, Processing and Attacking of Signle Sign-On Protocols with Burp Suite](https://www.nds.rub.de/media/nds/veroeffentlichungen/2015/10/30/OIDS-EsPReSSO.pdf)
- Paper: [Your Software at my Service (Signature Faking)](https://www.nds.rub.de/media/ei/veroeffentlichungen/2014/11/11/SaaSSAMLSecurity_CCSW2014.pdf)
- Paper (OpenID): [Do not trust me: Using malicious IdPs for analyzing and attacking Single Sign-On](https://cryptome.org/2014/12/do-not-trust-me.pdf)
- Papers and Thesis': [Various](https://www.nds.rub.de/teaching/theses/completed/)

##### Tools

- [WS Attacks](http://www.ws-attacks.org/Welcome_to_WS-Attacks)
- [WS-Attacker](https://github.com/RUB-NDS/WS-Attacker)
- [SSOScan (OAuth-based protocols)](http://ssoscan.org/vulnerabilities)
- BurpSuite Extensions
  * [EsPRreSSO:](https://github.com/RUB-NDS/BurpSSOExtension0
  * [SAMLRaider (Manipulating SAML Messages, manage x.509 certificates):](https://github.com/SAMLRaider/SAMLRaider)

#### Defensive

- SAML Artifact Binding (Thwarts most XSW attacks), [OASIS SAML v2 Section 4.1.3](https://www.oasis-open.org/committees/download.php/20645/sstc-saml-tech-overview-2%200-draft-10.pdf)
