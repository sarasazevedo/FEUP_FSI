# Logbook 11

## Lab Environment 

Initially, we executed the commands and add the new entries provided in the lab. 

```
sudo nano etc/hosts     # colocar '10.9.0.80 www.bank32.com' e ’10.9.0.80 www.smith.2020.com’ 

dcbuild               
dcup                  
``` 

## Task 1

To complete this task, we copied the default certificate present in /usr/lib/ssl/openssl.cnf to the local working folder. Then we uncommented the “unique _subject” line to allow creation of certifications with the same subject, and ran the following commands to create our environment

![Alt text](/images/logbook11_1.png)

After that we ran the following command to generate the self-signed certificate for the CA: 

![Alt text](/images/logbook11_2.png)

To complete that process, we put the following data:
- Passphrase : dees
- Country : PT
- State : Porto
- Locality : Paranhos
- Organization Name : UP
- Organizational Unit Name: FEUP
- Name : T05g07
- Email : aluno@fe.up.pt
 
To look at the decoded content of the X509 certificate and the RSA key we used:

```
openssl x509 -in ca.crt -text -noout 
openssl rsa -in ca.key -text -noout 
``` 

![Alt text](/images/logbook11_3.png)

![Alt text](/images/logbook11_4.png)


Answering the questions: 

•	What part of the certificate indicates this is a CA’s certificate? 

We can see that it is a CA certificate since there is a true certificate authority attribute in the basic constraints section

![Alt text](/images/logbook11_5.png)

•	What part of the certificate indicates this is a self-signed certificate? 

This certificate is self-signed because the issuer field and the subject field are the same

![Alt text](/images/logbook11_6.png)

•	In the RSA algorithm, we have a public exponent e, a private exponent d, a modulus n, and two secret numbers p and q, such that n = pq. Please identify the values for these elements in your certificate and key files.

We could identify the following elements:

The public and private exponents (publicExponent and privateExponent fields):

![Alt text](/images/logbook11_13.png)

![Alt text](/images/logbook11_14.1.png)

![Alt text](/images/logbook11_14.2.png)

The modulus (modulus field):

![Alt text](/images/logbook11_11.1.png)

![Alt text](/images/logbook11_11.2.png)

The two prime numbers (prime1 and prime2 fields): 

![Alt text](/images/logbook11_7.png)

![Alt text](/images/logbook11_8.png)

## Task 2

First, we use the command given to generate a CSR for www.bank32.com. 

![Alt text](/images/logbook11_15.png)

To see the results we ran `openssl req -in server.csr -text -noout` and `openssl rsa -in server.key -text -noout`

![Alt text](/images/logbook11_16.png)

![Alt text](/images/logbook11_17.png)

To conclude the task we have to add two more names for www.bank32.com so we used the following command: 

```
openssl req -newkey rsa:2048 -sha256 \
-keyout server.key -out server.csr \
-subj "/CN=www.bank32.com/O=Bank32 Inc./C=US" \
-addext "subjectAltName = DNS:www.bank32.com, DNS:www.bank32A.com,
DNS:www.bank32B.com" \
-passout pass:dees
```

![Alt text](/images/logbook11_18.png)

## Task 3

To generate a certificate for our server www.bank32.com, we had to run the following command:

```
openssl ca -config openssl.cnf -policy policy_anything -md sha256 -days 3650 -in server.csr -out server.crt -batch -cert ca.crt -keyfile ca.key
```

![Alt text](/images/Logbook11-N1.png)

Answering the questions:

•	What part of the certificate indicates this is a CA’s certificate?

We can see that it is a CA certificate since there is a true certificate authority attribute in the basic constraints section

![Alt text](/images/Logbook11-N2.png)

•	What part of the certificate indicates this is a self-signed certificate?

This certificate is self-signed because the issuer field and the subject field are the same

![Alt text](/images/Logbook11-N3.png)

•	In the RSA algorithm, we have a public exponent e, a private exponent d, a modulus n, and two secret numbers p and q, such that n = pq. Please identify the values for these elements in your certificate and key files.

We could identify the following elements:

The public and private exponents (publicExponent and privateExponent fields):

![Alt text](/images/Logbook11-N4.png)

![Alt text](/images/Logbook11-N5.png)

![Alt text](/images/Logbook11-N6.png)

The modulus (modulus field):

![Alt text](/images/Logbook11-N7.png)

![Alt text](/images/Logbook11-N8.png)

The two prime numbers (prime1 and prime2 fields):

![Alt text](/images/Logbook11-N9.png)

![Alt text](/images/Logbook11-N10.png)

## Task 4

For this task 4, we need to deploy a website with a certificate.

In order to make the website work, we needed to enable Apache’s ssl module and then enable this specific website. since they were already executed when the container is built, we dont need to use any commands to do that, so we can just launch the website from inside the container with the following command:

![Alt text](/images/Logbook11-N11.png)

When we access the website www.bank32.com we get a warning informing us that the connection is not secure, this happens because while our certificate is being recognized, it is from an unknown CA. 

To solve this warning we needed to add the file "ca.crt" to Settings -> Privacy & Security -> Certificates -> View Certificate.
