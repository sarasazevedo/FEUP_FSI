# CTF 13 

First we organized the information in wireshark, making it possible to visualize all the "Client Hello" frames. This made it pretty easy to find the frame that contained the number `52362c11ff0ea3a000e1b48dc2d99e04c6d06ea1a061d5b8ddbf87b001745a27`, which was in frame 814.

Using this frame, we were able to filter by the stream with the appropriate filter `tcp.stream eq 54` and added `tls.handshake` at the end to see what frames were part of the handshake.

Through this process, we were able to identify the first frame, frame `814`, and the last one, frame `819`. In frame `814`, we found two ciphers, but we confirmed the use of `TLS_RSA_WITH_AES_128_CBC_SHA256` by examining the ciphers used by the `Server Hello`.

Subsequently, to find the size of data that was transferred by the appdata, we added a column with the following feature: 
`tls.record.length` and associated it with the filter `tls.record.content_type == 23`. This resulted in the sum of the values of the added column, which was `1264`.

Finally, to discover the value of the encrypted message, we simply looked at the last frame of the handshake in TLS, that had the descryption `Encrypted Handshake Message`, and observed the value of its length, which was `80`.

As a result, the final flag was formed:

```note
flag{814-819-TLS_RSA_WITH_AES_128_CBC_SHA256-1264-80}
```