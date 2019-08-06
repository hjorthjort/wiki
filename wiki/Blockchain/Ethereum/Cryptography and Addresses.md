An ethereum address is the `0x` followed by 20 hex bytes.
The hex bytes are the rightmost 20 bytes of the (Keccak-256) hash of the public key.
Addresses are inherently case-insensitive.
However, for safety reasons the addresses are most often given with both upper and lower case letters.
The upper/lower scheme works like this:
1. Let `addr` be: remove the leading `0x`, and take the lowercase *string* representation of the address (not the byte representation).
2. Let hash be: Keccak hash `addr`
3. `addr` contains 40 hex letter (20 bytes), hash contains 64 letters (32 bytes). For `i` in `0..19`, if `hash[i]` is 0-7, lowercase `addr[i]`, and if `hash[i]` is 8-f, uppercase `addr[i]`.

Sources:
- https://en.wikipedia.org/wiki/Ethereum#Addresses
- https://forum.ethereum.org/discussion/9220/eth-address-upper-and-lower-characters-does-not-matter
