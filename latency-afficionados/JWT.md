# JWT

- Compact and self-contained way of transmitting data as a JSON object
- The information is digitally signed and can be trusted and verified based on that
- JWTs can be signed using a secret (HMAC algorithm) or a public/private key pair (RSA or ECDSA)
- 

JSON Web Tokens consist of three parts, separated by dots (xxxxx.yyyyy.zzzzzz):

1. Header
  - Usually consists of two information:
    - Type of the token (JWT)
    - Signing algorithm (HMAC, SHA256, RSA)
  ```JSON
    {
      "alg": "HS256",
      "typ": "JWT"
    }
  ```
  - This json is base64 encoded before added to the token
2. Payload
  - Contains the claims (data) to be transmitted
  - There are three different types of claims:
    - **Registered claims:** Predefined, but not mandatory claims
      - iss: issuer of the JWT
      - exp: expiration time
      - sub: subject of the JWT
      - aud: audience / recepients of the token
    - **Public claims:** Public comsumption data that might contain generic information (name, profile, etc)
      - Should use collision-resistant names
      - [IANA JSON Web Token Claims Registry](https://www.iana.org/assignments/jwt/jwt.xhtml#claims) contains examples of public registered claims
    - **Private claims:** Information specific to the application (employee ID, department, etc)
  ```JSON
    {
      "sub": "1234567890",
      "name": "John Doe",
      "admin": true
    }
  ```

3. Signature
  - 
