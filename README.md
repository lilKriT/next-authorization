# Next Auth

A small project in Next 13 where I am learning how cookie based authorization works. (no NextAuth!)

[Some links](#links)

Created by lilKriT.

# Auth

## What is \*_Auth_

- Authentication - is it really me?
- Authorization - am I allowed to do what I am trying to do?

## Where does Auth happen?

On the **server** inside **requests**

## What about client?

- Clients make requests
- Those requests can be authenticated and authorized

## How to do it in the front-end?

- Make a request
- for example `/api/me` or `/api/current-user`

# Flow

## Getting authenticated

- user opens the page for the first time
- user **signs in** - aka fires a request to server that returns a secure cookie
- client stores that cookie (in a way that can't be accessed by js)
- from now on all requests to the server will include the cookie

## Getting authorized

- user makes a request for data (or anything else that requires authorization)
- server receives request and parses the cookie
- server checks what user cookie is for
- server checks if user from cookie is authorized for this action
- is user is authorized, server returns data. Otherwise, error

This is not something you do on front-end in context provider.
Auth provider should be really "dumb". It should only have info if you are authed or not, and if there's a session with your name etc
Don't put too much stuff in your JWT

# Links

[https://www.youtube.com/watch?v=nI8PYZNFtac](https://www.youtube.com/watch?v=nI8PYZNFtac)

Some info:
**JWT** is for identification after authentication.
You should get **access** and **refresh** token.

**Access Token** - Short time (5-15mins)
**Refresh Token** - Long time (A few hours, day or more)

Main hazards
**XSS** - cross site scripting
**CSRF** - cross site request forgery

## Access Token

Your API should send **access token** as **JSON / JWT**
It should be stored in memory
**NOT** in local storage or cookie
It will be lost when app is closed (by design!), also known as current application state

## Refresh Token

Sent as **httpOnly cookie**
Not accessible by JS
Must have an expiry date!
Refresh token should NOT have the ability to issue new refresh tokens (that defeats the purpose)
