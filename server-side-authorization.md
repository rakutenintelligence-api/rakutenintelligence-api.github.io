---
layout: page
title: Server-Side Authorization
permalink: /server-side-authorization/
---

For this type of authorization, Rakuten Intelligence will be provide you with an API key and you will generate a DSA public/private keypair. Enter ONLY the public key into the Rakuten Intelligence developer portal.

# Usage
Under the server-side authorization method, accessing all collection and instance resources requires a request header called X-Slice-API-Signature.

This header should contain the following parameters in URL query-string format:

- **client_id**: the client id issued to this partner (previously called "apiKey")
- **timestamp**: the timestamp of the request, in milliseconds (must be no more than 30 seconds old)
- **username**: the user name associated with this request
- **client**: for third-party developers, this is always "p"
- **request_signature**: a Base64-encoded SHA1 signature of the concatenation of HTTP method, request path, client_id, timestamp, and username (if applicable), generated with your private key. Make sure to URL-encode this parameter, as it may contain special characters.

For example, if there is no user name on the request, the string used to generate the request signature looks like `GET /api/v1/usersabcd1234123456789123` (assuming `abcd1234` is the client id and `123456789123` is the timestamp in milliseconds).

If there is a user name on the request, the string used to generate the request signature looks like `PUT /api/v1/items/12133232321312312abcd1234123456789123victor` (assuming `/api/v1/items/12133232321312312` is the resource being requested, `abcd1234` is the client id, `123456789123` is the timestamp in milliseconds, and `victor` is the username)

Note that in this type of authorization, you are responsible for helping your users link their mailboxes to Rakuten Intelligence using the mailbox-management APIs. See also [Mailbox Management with Server-Side Authorization]({{ '/mailbox-management' }})

# How to generate a public/private keypair?

Note: if you're not able to run this, we can provide a standard Docker image and instructions to generate the keys.

At a UNIX or Linux command-prompt, execute the following command to generate a private key:

    ssh-keygen -q -t dsa -b 1024 -f mykeypair

That should generate a file called "mykeypair", which contains your private key. To get a private key in PEM format, run the command below:

    openssl dsa -in mykeypair -outform pem > myprivatekey.pem

This generates a file called "myprivatekey.pem", the private key certificate you will use to sign your requests.

Execute the following command to generate a public key from the private key binary:

    openssl dsa -in mykeypair -pubout -out mypubkey.pem

That will generate a file called "mypubkey.pem".

Now run the following command to get the entire public key on a single line:

    grep -v "PUBLIC" mypubkey.pem | tr -d '\n'; echo

Copy-paste the output from that command into the developer portal. Make sure to store the private key securely. You will be using it to sign your API requests.
