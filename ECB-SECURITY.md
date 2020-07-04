#  Enterprise Chat Bridge (ECB) - <em>Understanding Security</em>
**ECB** is stateless; it identifies users and message threads using JWTs (JSON Web Tokens).  

To achieve this, your server <ins>signs</ins> tokens identifying users and threads before sending them to **ECB**, which in turn forwards them to your web clients. 

When your web clients make any request (send messages, get threads, etc...) **ECB** verifies the authenticity of those requests by examining the tokens before processing them. 

To improve security, **ECB** only supports RS256 which is an asymmetric algorithm. 

Data can only be <ins>signed</ins> using the **private key (secret)**, so this key is stored in your backend application.

The **public key** will be given to both **ECB** and your web client, they will use it to verify and decode tokens. 

