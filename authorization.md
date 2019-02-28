---
layout: page
title: Authorization
permalink: /authorization/
menu: main
---

We always recommend that developers start out using our OAuth authorization method. Besides using the simple, widely-used OAuth 2.0 authorization method, this allows a very lightweight integration. Since OAuth simply allows users to share their existing Rakuten Intelligence accounts with you (or create new Rakuten Intelligence accounts and share them with you), it doesn’t require you to think about the actual logistics of linking mailboxes or keeping them linked. Best of all, the OAuth method is free for an unlimited number of linked user accounts!

The server-side integration method, by contrast, offers a seamless integration between your app and the mail providers, powered by Rakuten Intelligence. If you choose a server-side integration, your users can access their data only through your app, not through the Rakuten Intelligence apps. This means that you are responsible for the fairly complex task of routing your users through the appropriate mailbox-linking flow, and for prompting your users to re-authenticate their mailbox if the access tokens stop working.

If you’re not sure of the practical difference here, we recommend that you start out with OAuth. It will be a quicker integration and will allow you to easily test out our API in your app. If you decide later on that you would rather do a deeper, server-side integration, we will happily support you in that.

There are two authorization methods available, depending on your use case. Authorization methods cannot be mixed and, since they require different credentials, cannot be changed:
- [OAuth 2.0 Authorization]({{ '/oauth-authorization' }})
- [Server-Side authorization]({{ '/server-side-authorization' }})