---
layout: page
title: Resources
permalink: /resources/
menu: main
---

- [Users]({{ '#users' }})
- [Mailboxes]({{ '#mailboxes' }})
- [Orders]({{ '#orders' }})
- [Items]({{ '#items' }})
- [Shipments]({{ '#shipments' }})
- [Merchants]({{ '#merchants' }})
- [Email]({{ '#email' }})
- [Static Resources]({{ '#static-resources' }})
- [Actions]({{ '#actions' }})

# Resources Overview
There are two primary types of resources:

- Instance resources contain exactly one piece of data. For example, the /api/v1/orders/{order_id} instance resource refers to exactly one order.

- Collection resources contain sets of data of a particular type. For example, the /api/v1/orders collection resource contains all of the orders for a particular user. Collection resources are represented as a JSON array of instance resources, and are sorted in descending order by their last updated times.

All of the resources in the Slice REST API are represented as JSON. The object models are defined on the individual resource pages.

Unless otherwise noted, all resources are in the context of a specific user, who must have authorized your app to access his or her data. This means that these resources must be called with either a single user id or a valid OAuth token that corresponds to a single user, and they will return only the data that applies to that user. For example, the /api/v1/orders collection resource, called with an OAuth token, returns only the orders that have been placed by that user.

PUT/POST/DELETE requests must be sent with the body in querystring notation. They are available for the following resources:

- /api/v1/users (for server-side integrations)
- /api/v1/orders
- /api/v1/items
- /api/v1/shipments
- /api/v1/merchants

# Users

The "users" resources contain meta-information about a Slice users has authorized the sharing of their data with a specific partner.

## Usage
Since the two Slice authorization methods differ at the user level, usage of these resources differs between the two authorization methods.

### OAuth
If you are using OAuth, you have access to the following instance resource:

**/api/v1/users/self**

The user object corresponding to the exact user identified by a valid OAuth token.

This resource is accessible by GET.

### White-label Authorization
If you are using server-side authorization, you have access to the following resources:

**/api/v1/users**

This is a collection resource representing all users that have registered through your app. When called with GET, it returns a JSON-formatted list and can be refined with the following URL parameters:

- since: only return those resources that have been created or modified since some particular timestamp (expressed as milliseconds since the UNIX epoch)

When called with POST, it causes our system to register a new user. The details of the user should be specified as URL parameters matching the field names below (under "Object Model").

**/api/v1/users/self?username={username}**

This is an instance resource representing the user object for a specific user, assuming that this user registered through your app. The {user_name} is any unique identifier that is meaningful to you, which you should use when creating the user with the Slice API. For example, it might be a primary key for the user account in your database, or a user name or email address from your app.

When called with GET, it returns a JSON-formatted object.

When called with PUT, it causes our system to update the user identified by the URL. The details of the user should be specified as URL parameters matching the field names below (under "Object Model").

When called with DELETE, it causes our system to delete the user identified by the URL. Use this with caution, as we will also delete all mailboxes and extracted data associated with that user!

## Object Model
A user object is expressed as a JSON object having the following fields:

- userName: the name that was used to identify this user when it was created. This field is only set for partners using server-side authorization, in which case it must be unique within the context of a specific partner.
- updateTime: the time at which the user was last updated, in milliseconds since the UNIX epoch.
- firstName: the first name of the user, if available.
- lastName: the last name of the user, if available.
- linkedAccounts: a list of linked accounts returned as a JSON-formatted array.
- userEmail: the primary email address used to register this user. This field is now deprecated and will be removed on April 1, 2016.
- mailboxes: links to mailboxes associated with the user, returned as a JSON-formatted array.
- href: a link to the resource for this user.
- createTime: the time at which the user was first created, in milliseconds since the UNIX epoch(Jan 1st, 1970).

A user object looks like the following:

    {
        "result" : {
            "userName" : null,
            "updateTime" : 1396976545000,
            "firstName" : "Keyser",
            "lastName" : "Soze",
            "linkedAccounts" : [],
            "userEmail" : "slice_test_user@yahoo.com", 
            "mailboxes : [
                {
                "href" : "https://api.slice.com/api/v1/mailboxes/-8894833144811083777",
                },
                {
                "href" : "https://api.slice.com/api/v1/mailboxes/-5886379017253881529",
                }
            ],
            "href" : "https://api.slice.com/api/v1/users/self",
            "createTime" : 1396976298000
        },
        "currentTime" :1396976543000
    }

# Mailboxes

The "mailboxes" resources contain mailbox information for a particular user.

## Usage
The following resources are available to all partners, in the context of a specific user:

- /api/v1/mailboxes: a collection resource that contains information about all linked mailboxes for the context user, which can be refined with the following parameter:
since: only return those resources that have been created or modified since some particular timestamp (expressed as milliseconds since the UNIX epoch).
- /api/v1/mailboxes/{mailboxes_id}: an instance resource that represents a particular mailbox, which must be accessible by the context user.
These resources are accessible by GET. In addition, the instance resource is accessible by DELETE, which causes us to delete a particular mailbox and stop crawling it.

For security reasons, mailboxes are not currently accessible by PUT or POST. For server-side partners only mailboxes can be added or edited using the mailbox-management API.

## Object Model
A mailbox object is expressed as a JSON object having the following fields:

- status: A JSON object containing the following fields:
- code: The HTTP status code returned by the server
- description: A description of the code
- updateTime: the time at which the mailbox was last updated, in milliseconds since the UNIX epoch.
- href: a link to the instance resource for this mailbox.
- user: a link to the "users" resource for the user to whom this mailbox belongs.
- provider: a link to the "mailproviders" resource identifying the provider that hosts this mailbox.
- email: the email address for this mailbox.

Below is a description of each status along with their meanings:

|Status|Code|Description           |
|-----------|---|------------------|
|OK         |200|Everything is OK! |
|ONBOARDING |201|Slice is still in the process of adding the mailbox into our system |
|UPDATING	|202|Slice is actively modifying the mailbox information |
|AUTH_ERROR	|400|Slice is experiencing problems authenticating with the provider. De-linking and relinking the account may fix this. |
|TEMP_ERROR	|500|Slice is experiencing a different issue and we’re working to get it fixed! |

The response looks like the following:

    {
        "result" : [
            {
                "status" : {
                "code" : 200,
                    "description" : "OK"
                },
                "href" : "https://api.slice.com/api/v1/mailboxes/-5886379017253881529",
                "lastUpdated" : "<null>",
                "updateTime" : 1396976298000,
                "user" : {
                    "href" : "https://api.slice.com/api/v1/users/self"
                },
                "provider" : {
                    "href" : "https://api.slice.com/api/v1/providers/-1"
                },
                "email" : "storage@slice"
            },
            {
                "status" : {
                    "code" : 200,
                    "description" : "OK"
                },
                "href" : "https://api.slice.com/api/v1/mailboxes/-8894833144811083777",
                "lastUpdated" : "2014/04/08 16:59:01",
                "updateTime" : 1396976375000,
                "user" : {
                    "href" : "https://api.slice.com/api/v1/users/self"
                },
                "provider" : {
                    "href" : "https://api.slice.com/api/v1/providers/1"
                },
                "email" : "test_slice_user@yahoo.com"
            }
        ],
        "currentTime" :1396976453000
    }

# Orders
The "orders" resources contain order-level information for a particular user.

## Usage
The following resources are available to all partners, in the context of a specific user:

**/api/v1/orders**
This is a collection resource that contains all order information for the context user.

When called with GET, it returns a JSON-formatted list and may be refined with the following optional URL parameters:

- since: only return those resources that have been created or modified since some particular timestamp (expressed as milliseconds since the UNIX epoch).
- showDeleted: if true, include those resources that have been marked as deleted. (Note that we will only show the "href" field for these resources.).
- limit: do not include more than this number of resources (this is used along with "offset" to facilitate pagination).
- offset: skip this many resources from the start of the list (this is used along with "limit" to facilitate pagination).

When called with POST, it causes our system to record a new order. The details of the order should be specified as URL parameters matching the field names below (under "Object Model").

**/api/v1/orders/{order_id}**
This is an instance resource that represents a particular order.

When called with GET, it returns a JSON-formatted object.

When called with PUT, it causes our system to update the order identified by the URL. The details of the order should be specified as URL parameters matching the field names below (under "Object Model").

When called with DELETE, it causes our system to delete the order identified by the URL.

## Object Model
An order object is expressed as a JSON object having the following fields:

- orderTitle: a string description of the order
- merchant: a link to the "merchants" dictionary resource corresponding to the merchant where this order was placed
- updateTime: the time at which the order was last updated, in milliseconds since the UNIX epoch.
- shipmentEmails: a JSON-formatted array of URL’s to different emails
- shipments: array of links to "shipments" resources for any shipments associated with this order.
- purchaseType: a link to the "purchasetypes" dictionary resource that identifies the type of this purchase.
- orderNumber: the merchant’s order number, if available
- items: array of links to "items" resources for the items in this order.
- orderTotal: the total value of the order, expressed as an integer (e.g. 395 means $3.95).
- shippingCost: the cost of shipping the order, expressed as an integer.
- orderTax: the total tax applied to the order, expressed as an integer.
- orderEmails: an array of links to the "emails" resource for the order’s associated email.
- href: a link to the instance resource for this order.
- mailbox: a link to the "mailboxes" resource corresponding to the mailbox where this order was found (which in turn corresponds to a user)
- orderDate: the date the order was placed.
- The response looks like the following:

        {
            "result" : [ {
                "orderTitle" : "",
                "merchant" : {
                    "href" : "https://api.slice.com/api/v1/merchants/99999"
                },
                "updateTime" : 1290312938239,
                "shipmentEmails" : [{
                    "href" : "https://qpi.slice.com/api/v1/emails/1920183912831290",
                }],
                "shipments" : [{
                    "href" : "https://api.slice.com/api/v1/shipments/3889983046853675789" 
                }],
                "purchaseType" : null,
                "orderNumber" : "CustomOrder_15134856",
                "items" : [{
                    "href" : "https://api.slice.com/api/v1/items/-9198847712796719512" 
                }],
                "orderTotal" : 0,
                "shippingCost" : 0,
                "orderTax" : 0,
                "orderEmails" : [{
                    "href": "https://api.slice.com/api/v1/emails/-192391238912839123"
                }],
                "href" : "https://api.slice.com/api/v1/orders/-5441729090682611070",
                "mailbox" : {
                    "href" : "https://api.slice.com/api/v1/mailboxes/6722059547598554576"
                },
            },  
            {
                "orderTitle" : "",
                "merchant" : {
                    "href" : "https://api.slice.com/api/v1/merchants/99999"
                },
                "updateTime" :1343781928000,
                "purchaseType" : {
                    "href" : "https://api.slice.com/api/v1/purchasetypes/10",
                },
                "items" : [{
                    "href" : "https://api.slice.com/api/v1/items/4740030905789907965" 
                }],
                "orderNumber" : "CustomOrder_21358859",
                "orderTotal" : 0,
                "shipments" : [ ],
                "shippingCost" : 0,
                "href" : "https://api.slice.com/api/v1/orders/3048728969094699527",
                "mailbox" : {
                    "href": "https://api.slice.com/api/mailboxes/-12901231273821389"
                },
                "orderDate" : "2012-08-01"
            }, 
            {
                "orderTitle" : "",
                "merchant" : {
                    "href" : "https://api.slice.com/api/v1/merchants/99999"
                },
                "updateTime" :1343781970000,
                "shipmentEmails":[{
                    "href": "https://api.slice.com/api/v1/emails/210293019283928"
                }],
                "shipments" : [ ],
                "purchaseType" : {
                    "href": "https://api.slice.com/api/v1/purchasetypes/2"
                },
                "orderNumber" : "CustomOrder_21358881",
                "items" :  [{ 
                    "href" : "https://api.slice.com/api/v1/items/3595222574732146239" 
                }],
                "orderTotal" : 0,
                "shippingCost" : 0,
                "orderTax" : 0,
                "orderEmails" : [{
                    "href": "https://api.slice.com/api/v1/emails/129381837198273"
                }],
                "href" : "https://api.slice.com/api/v1/orders/6955251230964125202",
                "mailbox" : {
                    "href" : "https://api.slice.com/api/v1/mailboxes/6722059544892554576"
                },
                "orderDate" : "2012-08-01"
            }],
            "currentTime" : 1397166362000
        }

# Items
The "items" resources contain information for individual items. Each item is associated with exactly one "orders" resource.

## Usage
The following resources are available to all partners, in the context of a particular user:

**/api/v1/items**
This is a collection resource that contains all of the items purchased by a particular user.

When called with GET, it returns a JSON-formatted list and may be refined with the following optional URL parameters:

- since: only return those resources that have been created or modified since some particular timestamp(expressed as milliseconds since the UNIX epoch).
- showDeleted: if true, include those resources that have been marked as deleted. (Note that we will only show the "href" field for these resources.)
- limit: do not include more than this number of resources (this is used along with "offset" to facilitate pagination).
- offset: skip this many resources from the start of the list (this is used along with "limit" to facilitate pagination).
When called with POST, it causes our system to record a new item. The details of the item should be specified as URL parameters matching the field names below (under "Object Model").

**/api/v1/items/{item_id}**
This is an instance resource that represents a particular item.

When called with GET, it returns a JSON-formatted item object.

When called with PUT, it causes our system to update the item identified by the URL. The details of the item should be specified as URL parameters matching the field names below (under "Object Model").

When called with DELETE, it causes our system to delete the item identified by the URL.

## Object Model
An item object is expressed as a JSON object having the following fields:

- category: Slice’s categorization for this item, an enum with the following fields:
- href: a link to the "category" resource associated with this item.
- name: the name of the category.
- parsingConfidence: a number between 0 and 100 representing the parsed data’s confidence interval
- updateTime: the time at which the item was last updated, in milliseconds since the UNIX epoch.
- shipments: array of links to "shipments" resources associated with this item.
- description: the description of the item from the email.
- orderEmails: an array of links to "emails" resources associated with this item.
- productCode: a reserved field for a merchant-specific, unique identier for the product, null if not present.
- purchaseDate: the date at which the item was purchased.
- price: the price of this item, expressed as an integer (e.g. 395 means $3.95)
- order: a link to the order resource associated with this item.
- priceHistory: the price history for this item, an array of objects with the following fields:
- timestamp: the time at which Slice detected the price change
- price: the price that we detected at this time, an integer representing number of pennies.
- productUrl: the URL of the product page for this item on the merchant’s site, if available.
- href:a link to the instance resource for this item
- returned: a boolean value set if item has been returned; default is ‘false’
- returnByDate: the last day to return this item, if available.
- shipmentEmails: an array of links to "emails" resources associated with the order’s "shipments" resources.
- imageUrl: a link to the image associated with this order.
- quantity: the number of identical items in the order.
- The response looks like the following:

        {
            "currentTime" : 1397166917000,
            "result" : [{
                "category": {
                    "href": "https://api.slice.com/api/v1/categories/5", 
                    "name": "Apparel & Accessories"
                }, 
                "parsingConfidence": 100, 
                "updateTime": 1408626257000, 
                "shipments": [], 
                "description": "\"EMPIRE\" 21-Inch Rolling Satchel Grey", 
                "orderEmails": [{
                    "href": "https://api.slice.com/api/v1/emails/4971733214941935187"
                }],
                "productCode": null,
                "purchaseDate": "2014-08-17", 
                "price": 5014, 
                "order": {
                    "href": "https://api.slice.com/api/v1/orders/6085738055703289568"
                }, 
                "priceHistory": [{
                    "timestamp": 1408579200000, 
                    "price": 3559
                }], 
                "productUrl": "http://www.overstock.com/search?keywords=15023618&SearchType=Header", 
                "href": "https://api.slice.com/api/v1/items/-5837483875633891511",
                "returned": false,
                "returnByDate": null, 
                "shipmentEmails": [], 
                "imageUrl": "https://djcgg0wrlysb3.cloudfront.net/35571/images/logos/280px/logoOverstock.png", 
                "quantity": 1
                } 
            ]
        }

# Shipments
The "shipments" resources contain information about shipments. Each shipment is associated with one or more "items" resources.

## Usage
The following resources are available to all partners, in the context of an individual user:

### /api/v1/shipments
This is a collection resource that contains all of the shipments that have been tracked for a particular user.

When called with GET, it returns a JSON-formatted list and may be refined with the following optional URL parameters:

- since: only return those resources that have been created or modified since some particular timestamp (expressed as milliseconds since the UNIX epoch).
- showDeleted: if true, include those resources that have been marked as deleted. (Note that we will only show the "href" field for these resources.).
- limit: do not include more than this number of resources (this is used along with "offset" to facilitate pagination).
- offset: skip this many resources from the start of the list (this is used along with "limit" to facilitate pagination).

When called with POST, it causes our system to record a new shipment. The details of the shipment should be specified as URL parameters matching the field names below (under "Object Model").

### /api/v1/shipments/{shipment_id}
This is an instance resource that represents a particular shipment.

When called with GET, it returns a JSON-formatted object representing the shipment.

When called with PUT, it causes our system to update the shipment identified by the URL. The details of the shipment should be specified as URL parameters matching the field names below (under "Object Model").

When called with DELETE, it causes our system to delete the shipment identified by the URL.

## Object Model

A shipment object is represented as a JSON object having the following fields:

- status: the status of the shipment as of this update, an enum with the following fields:
- code: a numeric id representing the status
- description: the name of the status, one of the following:
    - Code 0: PENDING (the shipment is registered but hasn’t left the warehouse yet)
    - Code 1: SHIPPED (the shipment has left the originating warehouse)
    - Code 2: OUT_FOR_DELIVERY (the shipment has left the final warehouse and is on its way to the destination)
    - Code 3: DELIVERED (the shipment has been delivered to its destination)
    - Code 4: ERROR (the shipment has some unusual status, which is described in more detail in the history)
    - Code 5: UNKNOWN (unable to retrieve the shipment status for some reason)
- updateTime: the time at which the shipment resource was last updated, in milliseconds since the UNIX epoch.
- shipper: represents the shipper carrying the shipment, such as FedEx.
- updateTime: time at which the shipper information was last updated, in milliseconds since the UNIX epoch.
- href: link to the resource with all information about this shipper.
- name: the name of the shipper.
- logoUrl: the link to an image containing the shipper’s logo.
- description: an item description for the shipment.
- receivedDate: the date at which the package was received.
- receivedShare: an array containing users that were notified of the primary user obtaining their package, null otherwise.
- trackingUrl: the URL to track the shipment, if available.
- shares: an array containing the list of users shipment has been shared with.
- merchantTrackingUrl: a URL containing the merchant tracking info.
- emails: an array containing links to the corresponding "emails" resources associated with the shipment.
- href: a link to the instance resource for this shipment.
- shippingDate: a string representing the date the item was shipped, otherwise null.
- items: array of links to "items" resources corresponding to the items in the shipment.
- shippingEstimate: the range of dates when the shipment may be shipped, if not already shipped.
    - minDate: the earliest date.
    - maxDate: the latest date.
- trackingNumber: the tracking number for this shipment.
- deliveryEstimate: the range of dates when the shipment may be delivered, if not already delivered.
    - minDate: the earliest date
    - maxDate: the latest date
- destinationAddress: the destination address, an object having the following fields:
    - locationString
    - postalCode
- history: the shipping history of this shipment, an array of objects having the following fields:
    - locationString: the location of the shipment at the time of this update.
    - timestamp: the time of this update
    - activity: any message included with the update.

The response looks like the following:

    {
        "currentTime" : 1397167045000,
        "result" : [{
            "status": {
                "code": 3,
                "description": "DELIVERED"
            }
            "updateTime" : 1346814629000,
                "shipper" : {
                    "href" : "https://api.slice.com/api/v1/shippers/2",
                    "name" : "UPS"
                },
                "description" : "ProCloset",
                "receivedDate" : "2012-09-04",
                "receivedShare" : null,
                "trackingUrl" : "http://wwwapps.ups.com/WebTracking/track?loc=en_EN&track.x=track&trackNums=1Z4F35V80362764965",
                "shares": [],
                "merchantTrackingUrl" : null,
                "emails" : [{
                        "href": "https://api.slice.com/api/v1/emails/-12191239712371293"
                    }
                ],
                "href" : "https://api.slice.com/api/v1/shipments/-3127808222139479801",
                "shippingDate" : "2012-08-27",
                "items" : [{ 
                        "href" : "https://api.slice.com/api/v1/items/-8117479253932305353" 
                    }
                ],
                "shippingEstimate" : {
                    "maxDate" : null,
                    "minDate" : null
                },
                "trackingNumber" : "1Z4F35V80362764965",
                "deliveryEstimate" : {
                    "maxDate" : "2012-09-04",
                    "minDate" : "2012-09-04"
                },
                "destinationAddress" : {
                    "locationString" : "SAN FRANCISCO, CA 94025, USA",
                    "postalCode" : "94025"
                },
                "history" : [{
                        "activity" : "BILLING INFORMATION RECEIVED",
                        "location" : "USA",
                        "timestamp" : "2012/08/27 09:33:23"
                    },
                    {
                        "activity" : "ORIGIN SCAN",
                        "location" : "PARSIPPANY, NJ, USA",
                        "timestamp" : "2012/08/27 18:14:00"
                    },
                    {
                        "activity" : "DEPARTURE SCAN",
                        "location" : "PARSIPPANY, NJ, USA",
                        "timestamp" : "2012/08/27 22:29:00"
                    },
                    {
                        "activity" : "ARRIVAL SCAN",
                        "location" : "HODGKINS, IL, USA",
                        "timestamp" : "2012/08/29 05:17:00"
                    },
                    {
                        "activity" : "DEPARTURE SCAN",
                        "location" : "HODGKINS, IL, USA",
                        "timestamp" : "2012/08/30 08:46:00"
                    },
                    {
                        "activity" : "ARRIVAL SCAN",
                        "location" : "SAN PABLO, CA, USA",
                        "timestamp" : "2012/09/02 05:34:00"
                    },
                    {
                        "activity" : "DEPARTURE SCAN",
                        "location" : "SAN PABLO, CA, USA",
                        "timestamp" : "2012/09/04 02:01:00"
                    },
                    {
                        "activity" : "ARRIVAL SCAN",
                        "location" : "SAN FRANCISCO, CA, USA",
                        "timestamp" : "2012/09/04 04:06:00"
                    },
                    {
                        "activity" : "OUT FOR DELIVERY",
                        "location" : "SAN FRANCISCO, CA, USA",
                        "timestamp" : "2012/09/04 04:45:00"
                    },
                    {
                        "activity" : "DELIVERED",
                        "location" : "SAN FRANCISCO, CA 94025, USA",
                        "timestamp" : "2012/09/04 19:39:00" 
                    }
                ],
            }
        ]
    }

# Merchants

The "merchants" resources contain information about the merchants in the Slice platform.

## Usage
The following resource is available to all partners:

**/api/v1/merchants/{merchant_id}**: an instance resource describing a particular merchant.
This resource is accessible by GET.

## Object Model

- updateTime: the time at which the merchant resource was last updated, in milliseconds since the UNIX epoch.
- name: the name of the merchant
- servicePhoneNumber: a phone number the merchant’s service department, if available.
- logoUrl: a URL that represents an image of this merchant’s logo.
- editable: a boolean representing whether this merchant can be edited
- websiteUrl: the primary URL for the merchant, if available
- href: a link to the instance resource for this merchant.
- priceDropPolicyUrl: a link to the merchant’s price-drop policy URL, if available
- serviceEmail: an email address for the merchant’s service department, if available.
- serviceFormUrl: the URL of the merchant’s customer-service form, if available
- returnPolicyUrl: a link to the merchant’s return-policy URL, if available
- createTime: the time at which the merchant resource was created, in milliseconds since the UNIX epoch.

The response looks like the following (sample data is truncated for brevity):

    {
        "result" : {
            "updateTime":1411689600000,
            "name":"Amazon",
            "servicePhoneNumber":"1-866-749-7539",
            "logoUrl":"https://example.org/images/logo.png",
            "editable":false,
            "websiteUrl":"http://www.amazon.com",
            "href":"https://api.slice.com/api/v1/merchants/1",
            "priceDropPolicyUrl":"",
            "serviceEmail":"",
            "serviceFormUrl":"",
            "returnPolicyUrl":"http://www.amazon.com/customer/display.html",
            "createTime" : 2112939123779
        },
        "currentTime" : 1396975419000,
    }

# Email

There are two instance resources that you can use to look up the emails associated with an order or shipment.

## Usage
We expose the meta-information of an email in the "emails" resource and it links to a sub-resource containing the actual relevant content from the email.

**/api/v1/emails/{EMAIL_ID}**

This instance resource corresponds to an email message. It contains meta-information from the email headers and links to a subresource containing the actual content from the email.

## Object Model
The email is represented as a JSON object with the following fields:

- from: the sender of the email
- emailType: either "order" or "shipment"
- content: a link to the /api/v1/emails/{EMAIL_ID}/content resource containing either the body of the email or the attachment corresponding to the receipt, which may be HTML, text, PDF, or Word
- to: the recipient of the email
- href: a link to the instance resource for this email.
- sentDate: the UNIX timestamp, in milliseconds, when the email was sent.
- subject: the subject line of the email

**/api/v1/emails/{EMAIL_ID}/content**

This is the "relevant content" of the email. If the receipt/shipment information was sent in an attachment with the original, we return the attachment itself with the appropriate Content-Type.

If the receipt/shipment information was in the body of the email, we return the body itself as HTML if available, otherwise we return it as plain text.

# Static Resources

Several resources in the Slice REST API refer to static dictionary resources.

**/api/v1/categories**

This resource represents the different categories of items that a user may have purchased.

It has the following fields:

- href: a link to the instance resource for this category
- name: the name of the category, e.g. "Home & Garden"

The response looks like the following (sample data is truncated for brevity):

    {
        "result" : [
            {
                "href" : "https://api.slice.com/api/v1/categories/1",
                "name" : "Other"
            },
            {
                "href" : "https://api.slice.com/api/v1/categories/2",
                "name" : "Books"
            },
            {
                "href" : "https://api.slice.com/api/v1/categories/3",
                "name" : "Baby Products"
            },
            {
                "href" : "https://api.slice.com/api/v1/categories/4",
                "name" : "Home & Kitchen"
            }
        ],
        "currentTime" : 1396974691000
    }

**/api/v1/providers**

This resource represents the different email providers that we support.

It has the following fields:

- domains: a list of internet domains that are all part of a larger company.
href: a link to the instance resource for this provider.
- preferred: whether or not this is the newest version of the provider’s service.
- name: the name of the provider, e.g. "gmail"
- key: the key corresponding to the provider’s database entry.

The response looks like the following (sample data is truncated for brevity):

    {
        "result" : [
            {
                "domains" : [],
                "href" : "https://api.slice.com/api/v1/providers/1",
                "preferred" : true,
                "name" : "Yahoo!",
                "key"  : "yahoo"
            },
            {
                "domains" : [
                    "gmail.com"
                ],
                "href" : "https://api.slice.com/api/v1/providers/2",
                "preferred" : true,
                "name" : "Gmail",
                "key"  : "gmail"
            },
            {
                "domains": [
                    "icloud.com", 
                    "me.com", 
                    "mac.com"
                ], 
                "href" : "https://api.slice.com/api/v1/providers/3",
                "preferred" : true,
                "name" : "iCloud",
                "key"  : "icloud"
            }
        ],
        "currentTime" : 1396975347000
    }
  
**/api/v1/purchasetypes**

This resource represents the different types of purchases.

It has the following fields:

- href: a link to the instance resource for this purchase-type.
- name: the name of the purchase type, e.g. "Shippable Order".

The response looks like the following (sample data is truncated for brevity):

    {
        "result" : [
            {
                "href" : "https://api.slice.com/api/v1/purchasetypes/100",
                "name" : "Other"
            },
            {
                "href" : "https://api.slice.com/api/v1/purchasetypes/2",
                "name" : "Shippable Purchase"
            },
            {
                "href" : "https://api.slice.com/api/v1/purchasetypes/3",
                "name" : "Payment"
            },
            {
                "href" : "https://api.slice.com/api/v1/purchasetypes/4",
                "name" : "Digital Download"
            }
        ],
        "currentTime" :1396975151000
    }
  
**/api/v1/shippers**

This resource represents the shippers that may be used to deliver packages.

It has the following fields:

- updateTime: the timestamp at which the shipper’s information was last updated, in milliseconds since the UNIX epoch.
- href: a link to the instance resource for this shipper.
- name: the name of the shipper, e.g. "FedEx".
- logoUrl: a link to an image containing the shipper’s logo.

The response looks like the following (sample data is truncated for brevity):

    {
        "currentTime" : 1396975221000,
        "result" : [
            {
                "updateTime" : 39012931990349,
                "href" : "https://api.slice.com/api/v1/shippers/1",
                "name" : "USPS",
                "logoUrl" : "http://usps.gov/logo.png"
            },
            {
                "updateTime" : 392930482399,
                "href" : "https://api.slice.com/api/v1/shippers/2",
                "name" : "UPS",
                "logoUrl" : "https://ups.com/logo.png"
            },
            {
                "updateTime" : 11112903928,
                "href" : "https://api.slice.com/api/v1/shippers/3",
                "name" : "FedEx",
                "logoUrl" : "http://www.fedex.com/logo.png"
            },
            {
                "updateTime" : 1,
                "href" : "https://api.slice.com/api/v1/shippers/4",
                "name" : "DHL",
                "logoUrl" : "http://www.dhl.com/logo.png"
            }
        ]
    }
  
# Actions

The actions resources are an assorted series of endpoints that developers can call to trigger certain internal Slice actions.

## For both White-label and OAuth Integrations

The following action is valid for both Server-side authorization and OAuth clients.

### Update API

Endpoint:

**/api/v1/actions/update**

Details: An action that can be called to trigger a crawl of the mailboxes of a user that has just logged in.

For server-side clients, there is a mandatory parameter that must be provided:
- username: the username to be updated.

With OAuth, the server is able to determine the user. As such, there is no need to provide the username.

#### Only White-label integrations

The following actions are valid for only Server-side authorization clients:

### Feed API

Endpoint:

**/api/v1/actions/feed**

An action that can be called to get a list of users with updates. This action can be refined with the following parameters:

Input Params:

- since: (Long) Returns the users which have updates after this timestamp (UNIX epoch milliseconds). If not provided then the api will return all the users
- offset: (Integer) The desired page number. Offset of zero returns the first page with the number of - objects mentioned in the limit(below field). Keep incrementing the offset until the number of objects - returned is less than the limit size or zero. By default the offset is zero.
- limit: (Integer) Maximum objects/users to be returned in one call. If there are a total of 800,000 objects a limit of 10,000 will only return 10,000 objects. Further objects need to be queried by incrementing the offset number. By default if no limit is provided all the objects/users will be returned.

Sample Response

    {
        "result": [
            {
                "updateTime": 1503353769000,
                "userName": "proxy1",
                "hasNewData": true,
                "mailboxesWithUpdates": [{
                    "href": "https://api.slice.com/v1/mailboxes/524415173634630469"
                }],
                "mailboxesWithAuthIssues": [{
                    "href": "https://api.slice.com/v1/mailboxes/524415173634630469"
                }]
            }
        ],
        "currentTime": 1503354201292
    }

Details: Responds with a collection of users’ meta data. Every element in the collection has the following attributes:

- updateTime: (Long) Will be deprecated soon.
- userName: (String) Partner provided username of the user
- hasNewData: (Boolean) Flag to show if there has been any updates to the users’ data since (see input param above) the timestamp. If there is only changes to mailbox status then the hasNewData will be false.
- mailboxesWithUpdates: (List) A list of mailboxes associated with this user which has updates
- mailboxesWithAuthIssues: (List) A list of mailboxes associated with this user which has auth issues

Gotchas:

- Please store separate since timestamps for Feed Api & for the User level calls(for Get Orders).
- The feed api might return hasNewData=true but when you query the orders for that user there is a chance that there might be no new orders due to some business policies hiding orders at runtime. But if feed API returns hasNewData=false you are assured that that user has no new data. This feature is provided to prevent making order calls when there is no new data to the user.

### Check For Updates API

Endpoint:

**/api/v1/actions/checkForUpdates**
An action to check if a specific user data has updates.

Input Params:

- since: (Long) The last timestamp (UNIX epoch) when there was an update to the users’ data. 

Note: Please keep using the same since timestamp until you find an update. The timestamp must be updated at your end only after you receive an update for your requests.

The mailbox ids associated with the user will be derived the from user request token or OAuth token etc.

Sample Response

    {
        "currentTime": 1505860569390,
        "result": {
            "success": true,
            "hasUpdates": true
        }
    }

Details: Responds with success and hasUpdates flags.

- success: (Boolean) Status of the request.
- hasUpdates: (Boolean) Flag showing whether the user data had updates since the since timestamp

**/api/v1/actions/login**
An action called to let user log into Slice.

**/api/v1/actions/logout**
An action called to let user log out of Slice.
