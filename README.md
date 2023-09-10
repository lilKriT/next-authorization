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

## Process

### Access Token

You get an Access Token issued during authorization
You can then access protected routes from API using the Access Token (AT) until it expires
API will verify AT with middleware everytime there's a request made
When AT expires, user application sends refresh token to the API refresh endpoint

### Refresh Token

Also issued at authorization
REST API endpoint will verify and cross reference the token in our DB.
This way we can terminate them early if user logs out.
Client uses them to get a new AT
Must be allowed to expire.

# Structure

**Backend** has 4 main methods and 4 routes:

- register
- log in (aka auth)
- refresh
- log out

Aka `/register`, `/login`, `/refresh` and `/logout`

**Controllers**

- register - get data from request. If missing, return. Check if user exists. If yes, return.Try: Encrypt password, create user. If works, return user, if not, return error.
- auth - get data from request, if missing, return. Find user. If none, return. Evaluate password. If doesn't match, return. Get user roles. Create **access token** with **username and roles**, and expires in **10 seconds**. Create **refresh token** with **username** nd expires in **1 day**. Save refresh token in database! Set **http only cookie** with refresh token. Send a response with **roles and access token**
- refresh - open cookies. if no token, return. Find user, if none - return. Try to verify token. If name different than in DB, return. Get roles from user. Create new access token with **username and roles** and **expires in 10 seconds**. Send response with **roles and access token.**
- logout - on client delete access token. Open cookies. If no token, return. Find user. Delete database refresh token. Clear cookie.

**Model**

Should contain whatever you need (name, id etc) and:

- roles - one way is to make it like this:

```js
roles: {
    User: {
        type: Number;
        default: somenumber
    },
    Editor: Number,
    Admin: Number
}
```

if the numbers don't match - User doesn't have that role.

- refresh token: a String

**Middleware**

Seems like there's 3:

- credentials (opens headers and checks user roles. If allowed origins include them, next().)
- verifyJWT (checks for **Bearer** token, tries to verify. If it works, next())
- verifyRoles

**Front End**
Whenever you send a request for anything that needs auth, first refresh your access token and then send with `useCredentials: true`

You might want to create your own function to make it easier.

If refresh expires, navigate user to login page. You can also send a `from` state property.

## Persitent user login

No need for localStorage / sessionStorage
However, it will not be as secure!

create PersistLogin componenet
import `outlet`, `state` and `effect`

Get refresh token
Wrap around your protected routes
