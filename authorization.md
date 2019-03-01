---
layout: page
title: Authorization
permalink: /authorization/
menu: main
---

There are two authorization methods available, depending on your use case. Authorization methods cannot be mixed and:

- [OAuth 2.0 Authorization]({{ '/oauth-authorization' }})
- [Server-Side Authorization]({{ '/server-side-authorization' }})

The **OAuth Authorization** method doesn't require the partner to implement the logistics of linking mailboxes and keeping them linked. It provides a simple, lightweight integration.

The **Server-Side Authorization** method, by contrast, offers a seamless integration between the partner's app and the mail providers, powered by Rakuten Intelligence. In this model, users can access their data only through the partner app, and not through the Rakuten Intelligence apps. This means that the partner is responsible for the task of routing their users through the appropriate mailbox-linking flow, and for prompting their users to re-authenticate their mailbox if the access tokens stop working.
