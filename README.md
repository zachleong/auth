# Authentication API
This is an authentication api which functions off of principles of the Diffie Hellman key exchange

With this authentication backend, you will not need to store any passwords, you will only server side secrets which are useless without the user secret and pin both of which are changed after every authentication.

The authentication process is done by comparing a common secret hash (created by a user and server secret) to a user hash.

##  Overview

The diffie-hellman exchange allows two people to establish a key to talk safely even when communicating within "ear-shot" of people who may wish to listen in on your secure channel. This technique (or at least a variation of it, elliptic-curve diffie-hellman) is the way that most secure communication is created on the internet.

This authentication api is a repurposing of the technique used and allows someone to be authenticated even when in "ear-shot" of people who may try to steal your information

## How it works:

### Initial signup
A common secret is generated by a user and server secret using the diffie hellman key exchange.

Two sets of random strings of bytes are generated using /dev/urandom, these bytes are then converted to integers which are exponentiated in base 2 and in mod of a secure prime number, as such:

common_secret = 2<sup>(user_secret * server_secret)</sup>  mod  PRIME_MODULUS

The prime modulus is chosen from https://www.ietf.org/rfc/rfc3526.txt (group id 14), however any secure diffie-hellman group can be chosen.

This common secret is then hashed and stored client side along with the user secret and can be protected behind a pin.

### Authentication process

The user secret and user hash are sent from the user and the common secret is calculated through diffie-hellman:

common_secret = (2<sup>(server_secret)</sup>)<sup>(user_secret)</sup>  mod  PRIME_MODULUS

The common_secret is then hashed and compared to the hash that the user provided and if it is a match then, the user is authenticated.
When the user is authenticated, a new user_half_secret with a new server_secret to generate a new common_secret which is again hashed and sent back to be stored client side.


Created by Zach Leong