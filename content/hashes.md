+++
title = "Hashing, the World of Message Digests"
date = 2023-09-27
weight = 1
+++

# Why can't you just send me my password?
If you have ever forgotten a password to a website before, you have surely thought to your self: "Why can't this website send me my password?" The short answer is: "the website does not know your password". That might shock you, but the website really **doesn't** know your password. The website knows the *hash* of your password. Hashes form the basis for a lot of security on the web and this post is dedicated to different hash functions. No prior knowledge in cryptography is assumed, but, basic math and some familiarity with the $\sum$ syntax, the modulus operator, binary representation, and exclusive or will be helpful. By the end of this post, you will know why a website can't just send you back your password.

# Why Store Hashes?
As a user, we don't want a website to store our passwords for two main reasons:
- If the website is hacked those attackers now have a list of known good usernames and passwords.
- A rogue site admin could take your password and username and sign in as you! If you reuse passwords (and I know you do) that same admin could now login to all those pages which share passwords without your knowledge.


# What is a Hash / Digest / Hash Function?
A hash function is a procedure for taking an input (or message) of any length, and producing a fixed length output. That output is called the hash or digest of the input. When we use hash functions in the real world we want to exclusively use **good** hash functions otherwise we start to have problems quickly. Hashes exist to make it possible to distinguish between two messages without looking at the messages. 

## An Ideal Hash
An ideal hash must:
- Given a message $M$ and hash function $H$, $H(M)=H(M)$
- Given a different message $D$, $H(M)\neq H(D)$
- $H(M)$ should be as small as possible such that the first two properties hold.
  

We want the first and second property because it allows us to check that two messages are the same by comparing their hashes. In the case of a website, the website can check if $H(\textrm{supplied password})=\textrm{stored password hash}$ to decide if you entered the right password, without ever knowing your password or the supplied password.


The third property of a hash is nice because using less data to verify them message is good. Using a smaller hash is faster to compute and to send over a transmission medium.

## What Makes a Hash Function Good?
Unfortunately, ideal (or sometimes called perfect) hash functions don't exist in the general case. So we use good-enough hashes in the real world. A good hash function has the following properties:
- When two messages have the same hash, this is called a collision. Hash Collisions are bad and a good hash function seeks to minimize them.
- A hash's distribution is how well it spreads the input space over the output space. We want a hash function with a distribution the spreads the inputs over the entire output space. 
- Fast (ish)
- The hashes of two messages should be very different even if the messages are very similar.
- Should always give the same hash or digest for the same message

## Difference Between a Cryptographic Hash Function and a Regular Old Hash function:
A hash function is considered *cryptographic* if given a message's hash, it is not possible to construct the original message. Cryptographers desire this property because they can then send $\textrm{hash}(\textrm{My secret key is password123})$ across an untrusted media like the internet without worrying about their secret being found out.

---

# A (Really) Bad Hash
This is the worst hash function I come up with, but its very simple. 
$$H(M)=1$$
Let's start with the positives:
- This hash function is really fast, in fact its hard to imagine a faster hash function. 
- This hash function also will always give the same hash for the same message. 
- This hash function cannot be reversed.

Looking at the definition of [good](#what-makes-a-hash-function-good) we can see where this fails. 
- All messages collide, which makes it useless to distinguish messages
- A small change to the input, has no change to the output
- This hash function does a poor job of spreading its outputs throughout the output space, by virtue of only having 1 output.

---

# An Improved Bad Hash
Addition is the first hash function one might come up with and it is bad for similar reasons. 

Let $\textrm{ascii()}$ be the [ascii](https://www.asciitable.com/) value of the character.

Let $M_i$ be the $i$th character in the message. For example: 

if $M=\textrm{Let's go Blackhawks}$ then $M_0=L$ and $M_4=\textrm{S}$   

$$H_2(M)=\sum_{k=0}^{\textrm{length}(M)}\textrm{ascii}(M_k)$$

This hash function is so easy its python implementation is just 5 lines:
```python
def hash2(msg:str) -> int:
    ret = 0
    for char in msg:
            ret += ord(char)
    return ret
```
$$H_2(\textrm{Paint the walls red}) = 1787$$
$$H_2(\textrm{I love winter}) = 1240$$

We are already doing better then our first hash. Two distinct messages have different hashes. Unfortunately without a tweak, we don't actually have a hash function.

## See if you can find why $H_2$ is not a hash function. 
<details>
<summary>Hint</summary>
Hash messages with very different lengths and see how many digits the corresponding hash has.
</details>
<details>
<summary>Hint 2</summary>
Can a hash function's output have different lengths?
</details>
<details>
<summary>Answer</summary>
$H_2$ is not a hash function because the digests that $H_2$ outputs are not fixed size! To show you this, lets hash two messages "A" and the full text of the Declaration of Independence.
$$H_2(\textrm{A})=65$$
$$H_2(\textrm{Declaration of Independence}) = 753,434$$
As the size of input grows, so does the size of the digest. A hash function must output a fixed size digest, and as a result $H_2$ is not actually a hash function. 
</details>

---

# An Improved Bad Hash (But that's actually a hash function)
Let's adjust the hash function to be:
$$H_3(M)=\left(\sum_{k=0}^{\textrm{length}(M)}\textrm{ascii}(M_k)\right)\mod 100$$
or the equivalent python code:
```python
def hash3(msg:str) -> int:
    ret = 0
    for char in msg:
            ret += ord(char)
    return ret % 100
```
If you don't know $x\mod n$ or in python `x % n` is saying "Divide $x$ by $n$ and give me the remainder". I chose 100 to keep numbers small but in practice we could operate mod anything.

Let's hash our messages again: 
$$H_3(\textrm{A}) = 65$$
$$H_3(\textrm{Declaration of Independence}) = 34$$
Great, neither of our hashes have launched into infinity and even though the Declaration of Independence is much longer it still fixed in size. Now that we have returned to fixed size land, let's see if our new hash function is *good*

Positives: 
- The distribution is pretty okay. For longer messages it will start to bounce around a lot which is good, but for short messages this hash won't be very good at distributing
- Summing and looking up in a ascii table is pretty fast. Mod is much slower to compute but its only done once so we will call the speed ok.
- This hash will always give the same value for the same message.
- It's practically impossible to reverse a message.

Problems:
- $H_3$ doesn't change much for messages which are very similar. For example $$H_3(\textrm{AA})=30$$ $$H_3(\textrm{AB})=31$$ We would prefer that the hashes of these two (and really any two) message be very different.
- Its trivial to find a message which collides. For instance: $$H_3(\textrm{AAAAD})=H_3(\textrm{BBBAA})=28$$
  
- As a matter of fact $H_3$ will only ever output 100 distinct hashes $[0, 99]$. That's thanks to our $\mod 100$, in practice we use much large hashes. For instance the current gold standard hash [sha-256](https://www.movable-type.co.uk/scripts/sha256.html) uses a 256-bit output space. I asked WolframAlpha to compute how many possible hashes a 256-bit output space can represent.
$$115,792,089,237,316,195,423,570,985,008,687,907,853,269,984,665,640,564,039,457,584,007,913,129,639,936 \textrm{ messages}$$
I see, that is quite a bit more than 100, thanks WolframAlpha. To truly wrap your head around the size of this number [this](https://www.youtube.com/watch?v=S9JGmA5_unY) video by 3Blue1Brown is great.

So would changing $\mod 100$ to $\mod 2^{256}$ fix the collision issue? No, it would just change what the colliding strings are. As a matter of fact "ZZZ" would then collide with "AAAK" $$H_3(\textrm{ZZZ})=H_3(\textrm{AAAK})=270$$

---

# A Good Hash Function
This is the full algorithm for a hash algorithm called FNV-1a
```python
FNV_PRIME = 16777619
FNV_OFFSET_BASIS = 2166136261

def fnv_1a(message:str) -> int:
    hash = FNV_OFFSET_BASIS

    for byte in bytes(message, "ascii"):
        hash = hash ^ byte
        hash = hash * FNV_PRIME

    return hash 

```
Starting from the top, `FNV_PRIME` is a specially chosen prime number which helps distribute the hash function through out the 32-bit output space. `FNV_OFFSET_BASIS` is a second prime chosen for similar reasons, and to make the hash of zeros non-zero.
```python
for byte in bytes(message, "ascii"):
```
Next, `for byte in bytes(message, "ascii"):` tells python to loop over every byte in the message and store that byte in `byte`. We get the bytes out of the message by taking the asci value of the character.


Then for each byte, we do two steps:
```python
hash = hash ^ byte
```
set the new state of the hash to the e**x**clusive **or** (XOR) of the current hash and the current byte of the message. This is what actually compresses our message into the hash.
```python
hash = hash * FNV_PRIME
```
Multiply the new hash (with our new byte of data) with the FNV_PRIME constant to spread the hash throughout the output space. and of course

This hash function is much better than any of our hash functions.
- `fnv_1a("fruit loops")` always equals `0x812559a844c8e9d0a75f1cb34a037966d755598dcd891e5d9032f4db0107b22d4bada04f26`
- Hashes of two messages which are very similar have different messages:
  - `fnv_1a("fruit loops") = 0x812559a844c8e9d0a75f1cb34a037966d755598dcd891e5d9032f4db0107b22d4bada04f26`
  - `fnv_1a("gruit loops") = 0x812559a744b7b44a11daa034bdd879ddf0e327b045099323be611037db85675b6a19bb6749`
- Collisions are hard to find.
- This is using quite a bit of its input space.
- Multiplication is pretty fast on computers and XOR is as fast as anything can be on a computer.
- Unfortunately is not cryptographic, it is possible to recover some data from the hash about the message.

---

# Conclusions
Hopefully now you are beginning to see how hard it is to make a good hash function. Literal years have gone into working out what is the best way to hash messages and we have two contenders:
- Blake2B, current fastest (good) hashing algorithm (as of late 2023)
- SHA-256, industry standard hash

As well as several vestigial "contenders":
- SHA1, SHA2, SHA3, predecessor versions of SHA-256. They have known efficient attacks to generate collisions
- MD5, an older hashing system
- CRC32, a message error detection function that uses many of the same ideas as hashing

## Who Cares About Bad Hash Functions?
Ideally you! We all uses hashes everywhere even if you don't know it. Almost every secure message you send online is protected by something called a [HMAC](https://en.wikipedia.org/wiki/HMAC) which is based on the hash function. Without it most modern secure communication could not occur. On your computer's boot is likely hashing important files to make sure they have not been corrupted. Any website which securely stores credentials uses hashes extensively.

---

# Further Reading
[How does SHA work?](https://www.youtube.com/watch?v=orIgy2MjqrA)
