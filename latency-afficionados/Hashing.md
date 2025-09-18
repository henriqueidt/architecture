# Hashing

- Hash functions are one-way functions, they can't be reversed to obtain the original input.
- A hash function will take a variable-length input and produce a fixed-length output, called `digest` or `hash`.
- Hash functions should be fast

## Avalanche Effect (collision resistance)
- Everytime the input of a hash function is changed, even a little bit, the output hash should change significantly. This is a desirable effect of cryptographic hash functions, as it ensures that similar inputs produce vastly different hashes.

## Cryptography

Not all hash functions are suitable for cryptography. To be used for Cryptography, a hash function must be:

- **Quick to compute**
- **One way**: it should be impractical or impossible to reverse the hash to obtain the original input.
- **Tamper resistant**: Any change to the input should produce a different hash (avalanche effect).
- **Collision resistant**: It should be impractical to have two different inputs producing the same output.

## Most used in cryptography
- **SHA-256**: Secure Hash Algorithm 2
  - Names are usually `SHA-` followed by the bit length (e.g., `SHA-256`, `SHA-512`).
- **SHA-3**
- **BLAKE2**

## HMAC
- HMAC (Hash-based Message Authentication Code) is a mechanism that combines a cryptographic hash function with a secret key.
- It provides data integrity and authenticity by ensuring that the message has not been altered and is from a legitimate sender.
