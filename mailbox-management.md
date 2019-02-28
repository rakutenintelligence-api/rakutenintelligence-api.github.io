---
layout: page
title: Mailbox management with server-side authorization
permalink: /mailbox-management/
---

In the white-label authorization world, since users won’t have access to their accounts through the Slice apps, you will have to allow them to link their mailboxes through your app.

In order to do this, please go through the following steps. This assumes you have already created a user and have the username that you used when creating the user.

## 1\. Get a request token for the user.

Make a POST request to /api/v1/actions/login with the signature parameters described [here]({{ '/server-side-authorization' }}).


The request will return a JSON object with a “requestToken” field. Use this token in step 2.

## 2\. Call Slice’s mailbox-management endpoint.

Redirect the user’s browser to /v2/manage_mailbox with the following parameters:

*   **rt:** the request token obtained in the previous step

*   **provider:** the REST URL identifying the mail provider, which can be obtained from the [providers]({{ '/resources/#static-resources' }}) resource

*   **mailbox:** the REST URL identifying the mailbox that needs to be updated, if applicable (optional)

*   **callbackUrl:** a callback on your side that we should call when the mailbox has been linked

Note that if the “mailbox” parameter is left empty, we assume this is a new mailbox being linked. If “email” is specified, we assume the user needs to re- authenticate and so update our access information for that email address.

## 3\. Invalidate the request token.

Now that the mailbox is linked, make a POST to  /logout with the following parameter:

*   **rt:** the request token obtained in the first step

This is an important step that cancels the request token and prevents anybody else from using it to access this user’s information.
