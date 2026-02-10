Cryptography Vulnerability
A cryptography vulnerability is a weakness in how encryption is designed, implemented, configured, or used, allowing attackers to read, modify, or forge protected data.

Objective
Each level has its own objective but the general idea is to exploit weak cryptographic implementations.

LOW:
the feature allows to encode and decode messages , in order to ensure a secure communication , so only sender and receiver should encode and decode the messages.
* pic here

first let's see what type if encoding is this.
reminder: Decoding hides data temporarily (reversible) , hashing destroys it into a fixed fingerprint. (not reversible)
from first sight : this Lg4WGlQZChhSFBYSEB8bBQtPGxdNQSwEHREOAQY= looks like base64. but when I tried decoding it :
* pic here

using magic function in cyberchef , is confirms it could be base64 :
* pic here

so maybe it's not an ordinary base64 , maybe it's combined with something else.
let's try something classic , like base64 with XOR:
* pic here

XOR hides data by flipping bits using a key, and the same key reveals it.

let's try another example:
*pic here 
and let's try decoding it using cyberchef:
* pic here

so they key is repetition of wachtwoord 
* pic here

using that key we can decode or encode whatever we like.
