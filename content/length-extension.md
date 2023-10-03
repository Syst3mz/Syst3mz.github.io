+++
title = "Use HMACs not Hashes"
date = 2023-09-27
weight = 1
draft=true
+++

I want to talk about everyone's favorite topic: cryptography. This is going to be a kind of a whirlwind of all of internet security from a cryptographic perspective so buckle up! No previous knowledge about cryptography is expected.

---

# The Cast
Following in the tradition of many cryptographers before us we will be using the following names:
- **Alice** wants to send some message to **Bob**, these two are the protagonists of the story.
- **Malory** is our antagonist; she has the power to sit between Alice and Bob and she can change any message sent from Alice to Bob.

<details>
<summary>"Malory can just change things? That seems unfair and unrealistic."</summary>
Good thinking, but unfortunately no. In this case, Malory has the same power as anyone preforming a <a href="[[Man In The Middle Attack]()](https://www.imperva.com/learn/application-security/man-in-the-middle-attack-mitm/)"><b>M</b>an <b>I</b>n <b>T</b>he <b>M</b>iddle (MITM)</a> attack. These attack are somewhat common, and, if you have ever heard an ad for a VPN service this is (the main) attack that VPNs can prevent!
</details> 

# The Problem
Alice wants to send a message to Bob. She wants to be sure that the message Bob receives is exactly the message that she sent.

---

This problem is non-trivial to solve, but if it pleases you try to find a protocol protocol that Alice and Bob can use to communicate. What follows is several protocols that can be used, building up to state-of-the protocols in use today. Probably also in the browser you are reading this on.


# Protocol 0
Well, the easy protocol is just to send the message.

[![](https://mermaid.ink/img/pako:eNpNjsEKwjAQRH-l7Ln9gRwERW_2ojfJZU22bTDJlk2KlNJ_N7Qo3obhvWEWMGwJFHSe32ZAydX1puPRO0NN01JK2JdwaNGzzP_NiZ9QQyAJ6GwZWHSsKg15oEAaVIkW5aVBx7Vw02gx08W6zAKqQ5-oBpwy3-doQGWZ6AudHfaC4UfRJrX7ze1tDSPGB3PYxfUDuAdDSg?type=png)](https://mermaid.live/edit#pako:eNpNjsEKwjAQRH-l7Ln9gRwERW_2ojfJZU22bTDJlk2KlNJ_N7Qo3obhvWEWMGwJFHSe32ZAydX1puPRO0NN01JK2JdwaNGzzP_NiZ9QQyAJ6GwZWHSsKg15oEAaVIkW5aVBx7Vw02gx08W6zAKqQ5-oBpwy3-doQGWZ6AudHfaC4UfRJrX7ze1tDSPGB3PYxfUDuAdDSg)

Remember we always include Malory, b/c she can sit in between and change anything.

## Flaws
I'm not sure this has to be explained, but for clarity lets do it. Malory doesn't have to send the message exactly as Alice sent it in.

[![](https://mermaid.ink/img/pako:eNpVjsEKgzAQRH8l7Nn8QA4FS3url_ZWctkmaw2aROIGEfHfG5QWehuY94ZZwURLoKAd4mw6TCxudx3qwRmSsmZG0wtkYXEOUp4aHGJa_oo89aU4xxdU4Cl5dLbMrToIoYE78qRBlWgx9Rp02AqXR4tMV-s4JlAtDhNVgJnjYwkGFKdMX-ji8J3Q_yjapeY4vX-vYMTwjNEf4vYBfSNH-w?type=png)](https://mermaid.live/edit#pako:eNpVjsEKgzAQRH8l7Nn8QA4FS3url_ZWctkmaw2aROIGEfHfG5QWehuY94ZZwURLoKAd4mw6TCxudx3qwRmSsmZG0wtkYXEOUp4aHGJa_oo89aU4xxdU4Cl5dLbMrToIoYE78qRBlWgx9Rp02AqXR4tMV-s4JlAtDhNVgJnjYwkGFKdMX-ji8J3Q_yjapeY4vX-vYMTwjNEf4vYBfSNH-w)

This is a bad protocol. Let's go to a better protocol.

# Solution 1
Let not just send the message $M$ but also the message's *hash* $H(M)$ so our new message is $$M||H(M)$$. 
Don't worry about the $||$ it just means concatenated, e.g. $\textrm{hello}||\textrm{world}=\textrm{"helloworld"}$.

