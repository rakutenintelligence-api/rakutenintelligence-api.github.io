---
layout: page
title: Authorization with Oauth 2.0
permalink: /oauth-authorization/
---

This document describes how to use OAuth to get authorization to access a particular user’s Slice data, and then how to authorize each subsequent API request with an OAuth access token.

## Provisioning on Slice

In order to receive OAuth access tokens on our partner-dev environment, simply click “Sign up for OAuth” on [our developer portal](https://developer.slice.com). We will provide you with a client id (GUID) and client secret. Please ensure that the client secret is stored in a secure manner.

Before getting started, log in to the developer portal and enter the URL for Slice to redirect back to at the end of the authorization flow. For security reasons this is the only URL that Slice will redirect to and it must match the **redirect_uri** parameter in the authorization steps detailed below.

## Getting Authorization from the User

This section describes the steps for integrating with our OAuth pages to get authorization from a Slice user to access their Slice data.

## Link the user to the Slice authorization page

The URL for the authorization page is https://api.slice.com/oauth/authorize and takes the following parameters (all are required unless otherwise noted):

*   **client_id:** the client ID (GUID) assigned by Slice.

*   **response_type:** the expected response type. Currently the only supported value is “code”.

*   **redirect_uri:** the URL that Slice will redirect back to after authorization is complete. Must be HTTPS, and must match the redirect_uri you have registered with Slice.

*   **state:** (optional) an arbitrary string that will be included in the response to your application.

## Slice login/signup/authorization UI

If the user is currently logged in to Slice, we will redirect them to the page to authorize your app. Otherwise we will first ask them to log in to their Slice account, or create a new Slice account, and then we will show our authorization page.

## Handle the response from Slice

If the user has authorized your app to access their Slice data, we will redirect the browser to the URL client specified in the original **redirect_uri** parameter, and will add the following parameters to the URL:

*   **code:** a short-lived authorization code that you can use to get access and refresh tokens (described below)

*   **state:** the same **state** parameter that was passed in step 1, if not empty

For example, if the redirect URL was https://www.myclient.com/callback, Slice would redirect to:

    http://www.myclient.com/callback?code={auth_code}&state={client_state}

If the user did not authorize your app to access their data, we will redirect to the same URL with an error code.

Errors are passed on the redirect URL using the following parameters:

*   **error:** a short form of the error name, one of the following:

    *   **access_denied:** user did not authorize your app to access their Slice data

    *   **unsupported_response_type:** if a response type is specified that Slice does not support

    *   **invalid_request:** if the original request is missing a required parameter, or a parameter has an invalid value

*   **error_description:** a long-form description of the error. Not always available.

For example, an error URL might look like this:

    http://www.myclient.com/callback?error=access_denied&error_description=The +denied+access+to+your+application

## Obtaining Access and Refresh Tokens

In the previous section, you obtained a short-lived authorization code. This code will expire after 60 seconds, and grants you permission to get access and refresh tokens for a specific user. Once you have the tokens, you can renew them to extend their lifetime indefinitely.

To get access and refresh tokens for this user, make a POST request to https://api.slice.com/oauth/token with the following parameters:

*   **client_id:** client id (GUID) assigned by Slice

*   **client_secret:** client secret assigned by Slice

*   **grant_type:** one of the following, depending on the case:

    *   **authorization_code:** if you don’t have tokens already, but you have an authorization code

    *   **refresh_token:** if you already have a refresh token and want to renew it and the access token

*   **code:** the authorization code (omit if using a refresh token)

*   **redirect_uri****:** the redirect URL specified in the authorization request (omit if using a refresh token)

*   **refresh_token:** the refresh token (omit if using an authorization code)

If the request is received in time (before the authorization code expires) and is valid in all aspects, you will receive a successful response containing a JSON object similar to the following:

<pre>    {
        "access_token": "b0575a44c66b21a8a6d7eb3f925ec97a",
        "expires_in": 3600,
        "token_type": "bearer",
        "refresh_token": "32e09c4a6c91cfbfd52e57d2eec43568"
    }
</pre>

If there is an error, you will receive an error response similar to the following:

### A Note on Tokens

Access tokens are generally valid for one hour, and the exact time to expiration is always specified in seconds in the **expires_in** field.

Once the access token has expired, you can use the refresh token to get a new set of access and refresh tokens. Refresh tokens are generally valid for fourteen days, but since one refresh token can be used to obtain another access and refresh token, you can keep a valid refresh token indefinitely. When you use a refresh token to obtain a new refresh token, the new refresh token replaces the previous one.

Please make sure to store the access and refresh tokens in a secure manner and in encrypted form.

### Authorizing Requests with an OAuth Access token

If you are using OAuth 2.0, you need to include the following request header on all API calls:

*   **Authorization: **a valid OAuth 2.0 request token that authorizes you to access data for a specific user. The token itself should have the “Bearer” prefix.

A typical authorization header will look like this:

    Authorization: Bearer NjY0MGRjN2RjZTZjZWJkMzVkYWYyZWU2OWQ2MDI2

Since an OAuth token corresponds to exactly one user, all resources are in the context of the user identified by that OAuth token.