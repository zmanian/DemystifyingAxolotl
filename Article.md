Demystifying Axolotl


Moxie and Trevor's Axolotol protcol represents the state of the art in applied cryptography to the problem of confidential messaging. In years since the Snowden revelations, there has been a lot of demand for privacy technolgies and reasonable amount of developer effort put behind which charitably falls short. I attribute a degree of this bikeshedding to ignorance. Axolotol employs a number of novel concepts that might disuade developers to implement something that seems more familiar. I will attempt to explain why Axolotol represents a beautiful design and why it deserves to be understood and not ignored.

This is not intended to document the Axolotol protocol from an implementation perspective. I'm instead hoping just to cover the high points of the design for wider appreciation and to invite more detailed study.


Threat Model:

The appropiate starting place for description of any cryptosystem is with the threats the system is designed to resist.

Axoltol is designed to preserve confidentiality of communications against an adversary that is able to record all bits on the network. The adversary is expected to be able to passively record all information known to the server.

The protocol is not designed to perserve confidentiality of metadata against the global adversary. The protocol assumes that end user devices are trustworthy. If an end user device is compromised, the protocol makes some guarantees of confidentiality of past communications but not future communications.

Cryptographic Components:


Elliptic Curve Cryptography

When designing a modern protocol requiring public/ private key pairs, ECC is the natural choice. While early Internet cryptosystems relied on RSA, ECC is better choice in virtually every respect. Ellitpic curve cryptography maps cryptographic functions to arethmetic identities on elleptic curves on prime fields. ECC has far fewer implementation nuances and complexities than RSA. Even if the underlying RSA algorithim is correct and a trusted random number generator is available, RSA provides numerous chances for catastrophic error. RSA keys have proven nearly impossible to generate without sidechannels while ECC keys are shorter integer values. ECC cryptographic primitives are expressed as simple arhtemtatic functions on the curve. Digging deeper is well worth your time. I reccomend Andreas intro ECC in his Bitcoin book[1]. Dan Bernstein gave a great intro to ECC talk at 31c3[2]. The TLDR is Axolotol uses ECC because it the right decision by a mile and not a meter.

Curve 25519

Curve25519 and its relative ed25519 are Dan Bernstein's most famous contributions to Ellptic Curves. The designer of the Elleptic Curves are in a position of profound resposnisbility. Selection of curve parameters can either fill a curve with booby traps or ease the life of implementers and users considerably. Curve25519 is designed to permit extremely fast and safe computation of shared secrets between two sets of key holders. Ed25519 uses the same prime but a different curve form optimized for quick generation and verification of edDSA signatures.

An extrordinary effort by Bernstein and his collaborators was made to select a curve which would ease implementation and move many potential security pitfalls out of scope. Curve25519 keys are short enough(256 bits) to tweet but offer security comparable to a 3072 bit RSA key.

Forward Secrecy

Forward Secrecy is an accomodation for the reality that against a powerful adversary, the compromise of end point devices is a strong possibility. It is also a reality that data sent over the network is near impossible to robustly erase. It is likely recorded somewhere whether the logs of an ISP or the NSA's Bluffdale data center.

Forward Secrecy is the notion that we use a different session key for each message and then forget that session key as soon as possible. Session keys should not be derivable from long term keys. Thus if a client device is compromised, the attacker cannot use the keys on the device decrypt bits on the wire they may have recorded. Forward Secret protocols require an adversary to transition from passive observer to active MITM. This active MITM can be detected by verifying key fingerprints.

Forward Secracy is an essential component of any modern messaging protocol.

Prior to Axolotol, there was no well established design pattern for asynchronous forward secret communication. Most forward secret proptocols require the power parties to be online to exchange ephemeral Curve25519 keys that will be used to generate an ephemereal session keys. Axoltol devised an approach where a server caches a set of ephemereal public keys for each user. Clients can then use these keys to encrypt a message with a temporary session key without a response from the recipient.


Triple Diffie Hellman

One of the challenges with encrypting with an ephemeral key is authenticating the identity AND establishing an ephemeral key. Many protocols do this in two steps and require a message exhange to first authenticate and then choose a session key.

Given two keys, ECDH computes a shared secret through a single aritemetic operation on a elliptic curve.

The Tripple Diffie Hellman is the combination of 3 diffie hellman operations. Mixing the results of diffie hellman operations on longer term keys and ephermeal keys produces a session key that provides proves the longer term identity of the recipient yet becomes immediately deniable once the private ephemereal key is forgotten.

Axolotol Communication Sequence
//TODO


Useful directions for future work

Since the Snowden revelations, many application developers have tried to meet market demands for privacy through new secure communication applications. Many of these proposal are crytpographic bikesheds that do nothing to improve the state of the art.




[1] https://github.com/aantonop/bitcoinbook/blob/develop/ch04.asciidoc


[2]Trevor talking at Cloud Flare
https://www.youtube.com/watch?v=8A9Eto9iqww