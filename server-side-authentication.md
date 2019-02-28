---
layout: page
title: Server-Side Authentication
permalink: /server-side-authentication/
---

For this type of authorization, Slice will be provide you with an API key and you will generate a DSA public/private keypair. Enter ONLY the public key into the Slice developer portal.

# Usage
Under the server-side authorization method, accessing all collection and instance resources requires a request header called X-Slice-API-Signature.

This header should contain the following parameters in URL query-string format:

- client_id: the client id issued to this partner (previously called “apiKey”)
- timestamp: the timestamp of the request, in milliseconds (must be no more than 30 seconds old)
- username: the user name associated with this request
- client: for third-party developers, this is always "p"
- request_signature: a Base64-encoded SHA1 signature of the concatenation of HTTP method, request path, client_id, timestamp, and username (if applicable), generated with your private key. Make sure to URL-encode this parameter, as it may contain special characters.

For example, if there is no user name on the request, the string used to generate the request signature looks like GET/api/v1/usersabcd1234123456789123 (assuming abcd1234 is the client id and 123456789123 is the timestamp in milliseconds).

If there is a user name on the request, the string used to generate the request signature looks like PUT/api/v1/items/12133232321312312abcd1234123456789123victor (assuming /api/v1/items/12133232321312312 is the resource being requested, abcd1234 is the client id, 123456789123 is the timestamp in milliseconds, and “victor” is the username)

In addition, the following parameter should be included on every request URL:

Note that in this type of authorization, you are responsible for helping your users link their mailboxes to Slice using the mailbox-management APIs.

# How to generate a public/private keypair?
At a UNIX or Linux command-prompt, execute the following command to generate a private key:

    ssh-keygen -q -t dsa -b 1024 -f mykeypair

That should generate a file called “mykeypair”, which contains your private key. To get a private key in PEM format, run the command below:

    openssl dsa -in mykeypair -outform pem > myprivatekey.pem

This generates a file called “myprivatekey.pem”, the private key certificate you will use to sign your requests.

Execute the following command to generate a public key from the private key binary:

    openssl dsa -in mykeypair -pubout -out mypubkey.pem

That will generate a file called “mypubkey.pem”.

Now run the following command to get the entire public key on a single line:

    grep -v "PUBLIC" mypubkey.pem | tr -d '\n'; echo

Copy-paste the output from that command into the developer portal. Make sure to store the private key securely. You will be using it to sign your API requests.

# Why use server-side authorization?

This is a deeper and more complicated integration than the OAuth integration, and it requires you to duplicate much of the Slice account management functionality, particularly around mailbox linking and management. If you create an app using server-side authorization, your users will not be creating Slice accounts as well, which means they will not be able to access their accounts in Slice. Furthermore, unlike OAuth, we only offer 10,000 users for free in server-side authorization, and we charge a per-user fee above 10,000 users.

You should consider using it if you are planning to build a white-label version of the Slice app, or if your policies do not permit using OAuth to redirect to Slice for account creation.