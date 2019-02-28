---
layout: page
title: FAQ
permalink: /faq/
menu: main
---

## Common Error Messages/Behavior for OAuth

**Q: Why did I receive the following error message: The client you are attempting to give access to is not authorized by Rakuten Intelligence. Please contact the developer?**

A: This message indicates that you have not been confirmed as a valid user. Please check the redirect_uri parameter to ensure it’s set to the correct value (https://developers.google.com/oauthplayground for our 'Quick Hello World’, or to your oauth callback URL).

## Common Error Messages for Server-Side

**Q: Why does the link_mailbox URL send me to the Slice login page?**

A: Your request token may not be URL safe. Try requesting another token by using api/v1/actions/login. If you still experience this behavior please contact us and the Slice API Team will resolve the situation.

**Q: What’s the easiest way to debug my server-side code?**

A: Simply download our command-line tool and use your credentials to populate the appropriate fields.

**Q: I made a request to api/v1/actions/login and it didn’t seem to work. Are there any additional steps that I need to take?**

A: To make the api/v1/actions/login request, you will need to have at least one user. Try linking mailboxes as they are added.

## Syntax for different request methods

**Q: What’s the syntax for making a PUT/POST/DELETE request for <Resource Name> ? I’ve tried sending as JSON and my changes don’t seem to go through?**

A: We require using the query string body notation for all PUT/POST/DELETE requests on Resources. Additionally, please make sure to specify the Content-Type header as Content-Type: "application/x-www-form-urlencoded".

**Q: Are there any sample requests available?**

A: As a matter of fact, there are! Please refer to this chart:

|Method|Example Slice CLT Request|Body contents|
|------|-------------------------|-------------|
|POST|POST /api/v1/orders "slicetestuser" orderDate 2010-02-22 orderTitle "Spotify Renewal"|	username=slicetestuser&orderDate=2010-02-22&orderTitle="SpotifyRenewal"|
|PUT|PUT /api/v1/orders/2812378237 "slicetestuser" orderDate 2016-02-25	|username=slicetestuser&orderDate=2016-02-25|
|DELETE|DELETE /api/v1/orders/2812378237 "slicetestuser"	|n/a|

## Syntax for Combining requests into one request

**Q: I currently make separate calls to \<Resource Name\> and \<Resource Name\>. Is there any way to concatenate the information so that I can access the information through one request?**

A: Yes, please use the expand URL query parameter along with the resource names(in a comma separated list).

Example: https://api.slice.com/api/v1/orders?expand=items,shipments

## Syntax for Filtering through response data

**Q: I have a very specific query for the Rakuten Intelligence API, is it possible to sort or remove some of the extraneous response data?**

A: Yes, it is. We’ve implemented some of the OData filter specification, and the supported features are as follows:

|Feature Type|Potential Values|Example|
|Comparison Operators|eq / ne / gt / ge / lt / le|"price GT 500"|
|Member Fields|"<Resource Type>/<Field Name>/<Subfield(s) Name(s)>"	|"order/merchant/name EQ 'Target’"|
|Logical Operators|and/or/not|"price GT 500 AND order/merchant/name EQ 'Target'"|
|Arithmetic Operators|add / sub / mul / div / mod|"price MOD 100 EQ 0"|
|String Functions|concat / substring / trim / contains / indexof / startswith / endswith / toupper / tolower|"CONTAINS(TOLOWER(merchant/name), 'hotel')"|
|Parentheses|()|"(price GT 5000) OR (price GT 1000 AND CONTAINS(description, 'foo'))"|

These strings should be called with the filter URL query parameter in the same manner as the expand parameter.

## Syntax for Ordering response data

**Q: I’d like to be able to view my results in a particular order. Would there be any way to ensure that?**

A: You can sort the results returned from the Rakuten Intelligence API by passing the orderBy URL query parameter. This parameter uses the same language as our filtering to display the API response in a particular way.

Example : https://api.slice.com/api/v1/orders?orderBy="purchaseDate desc, description asc, orderTax add ShippingCost"

## Method to uniquely identify request

**Q: I need to contact the Rakuten Intelligence API Team, but I don’t have any way of identifiying the API request I’m experiencing issues with. Is there a way I can do so?**

A: You can send the reqId URL parameter with a GUID for your Rakuten Intelligence API requests. Then, simply include the reqId for the request you have questions about to our team, and we’ll take it from there!

Example : https://api.slice.com/api/v1/users/self?reqId=2dk23j-2383jvhb-12i3jgh3
