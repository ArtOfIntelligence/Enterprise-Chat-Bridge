## Enterprise Chat Bridge (ECB)

# Operations Breakdown

This document explains the details of every operation **ECB** offers in the natural flow of using it.

## Operations

0. Get Access Token
1. Authenticate: `getUserToken`
2. Get User Contacts: `getContacts`
3. Get Threads: `getThreads`
4. Create New Thread: `createThread`
5. Get Thread: `getThread`
6. Send Message: `sendMessage`

### 0. Get Access Token

This operation takes place between your backend server and your client, outside the context of **ECB**.

The goal of this step is to place a valid access token in your web client, so that your client can get authenticated through **ECB**.

The access token should:

- Be encrypted
- Contain the user's id
- Have an expiry date/time to improve security

#### Server-Side Rendering

If you are web client pages are server-side rendered (such as ASP.NET, PHP, etc...) you can generate the access token for your user on the server, and place the token in the rendered page.

You may use an HTML hidden field for example:

```html
<input type="hidden" id="ecb-access-token" value="HA8-SJD8V-A23..." />
```

#### Static Pages

If your web client pages are static HTML pages, you will need to create an endpoint resource on your server that can be called by the client to retrieve the access token.

### 1. Authenticate: `getUserToken`

This operation essentially authenticates the user with **ECB**, and opens the two-way connection between your web client and **ECB**.

1. _Client:_ sends the `accessToken` to **ECB**
2. **_ECB:_** forwards `accessToken` to your server
3. _Your server:_ validates the `accessToken`
4. _Your server:_ creates and signs a `userToken` and returns it to **ECB**
5. **_ECB_** returns the `userToken` to your client
6. _Your client:_ initiates a two-way connection with **ECB** using the `userToken`

At this point, your web client can make requests to **ECB**, which in turn is able to verify those requests before processing them.

### 2. Get User Contacts: `getContacts`

This operation delivers an array of `users` that your web client can use to start new `threads` (conversations) with.

1. _Client:_ sends `getContacts` request to **ECB**
2. **_ECB:_** verifies `userToken`
3. **_ECB:_** forwards `getContacts` request to your server
4. _Your server:_ creates an array of `users` and returns it to **ECB**
5. **_ECB:_** transforms the array of `contacts` to a `paged` result set
6. **_ECB:_** returns the `paged` result set to your client

> If you are building a **peer-to-peer chat** system:
> You would - for example - return the requesting user's <ins>friends</ins> or <ins>employees</ins>.

> If you are building a **customer support** system:
> If the requesting user is a support <ins>agent</ins>, you would return an empty array, since your <ins>agent</ins> is not expected to initiate contact (start a new thread) with customers.
> But if the requesting user is a <ins>customer</ins>, you would return an array of ticket types for example.

### 3. Get Threads: `getThreads`

This operation delivers an array of `threads` that your web client can display on the UI, and the user can use to open a certain `thread`.

1. _Client:_ sends `getThreads` request to **ECB**
2. **_ECB:_** verifies `userToken`
3. **_ECB:_** forwards `getThreads` request to your server
4. _Your server:_ creates an array of `threads` and signs each `thread` with a `threadToken`
5. _Your server:_ returns the signed array of `threads` to **ECB**
6. **_ECB:_** transforms the array of `threads` to a `paged` result set
7. **_ECB:_** returns the `paged` result set to your client

### 4. Create New Thread: `createThread`

In this operation the user starts a new `thread` targeting one of the user's contacts, and includes a first `threadMessage`.

1. _Client:_ sends `createThread` request to **ECB**
2. **_ECB:_** verifies `userToken`
3. **_ECB:_** validates request body
4. **_ECB:_** forwards `createThread` request to your server
5. _Your server:_ creates the new `thread` and a new `threadMessages` , and stores them in your data store
6. _Your server:_ signs the new `thread` with a `threadToken`
7. _Your server:_ returns the results to **ECB**
8. **_ECB:_** returns results to your client

### 5. Get Thread: `getThread`

This operation return a single `thread` to your client, along with all its `threadMessages`. It also opens a two-way communication channel between the client and **ECB**, allowing **ECB** to [PUSH] new messages to the client.

1. _Client:_ sends `getThread` request to **ECB**
2. **_ECB:_** verifies `userToken`
3. **_ECB:_** verifies `threadToken`
4. **_ECB:_** If **ECB** is configured to block requests to `threads` where the `user` is not a member of that `thread`, **ECB** checks `thread` members first
5. **_ECB:_** forwards `getThread` request to your server
6. _Your server:_ fetches the `thread` and its `threadMessages`
7. _Your server:_ signs the `thread` with a `threadToken`
8. _Your server:_ returns the results to **ECB**
9. **_ECB:_** creates a private two-way channel for the `thread` and adds the `client` to this channel
10. **_ECB:_** returns results to your client

### 6. Send Message: `sendMessage`

When a user sends a new message to a `thread`, **ECB** is responsible for 1) sending it to your server for storage, then 2) pushing it to all connected clients who are listening to that `thread`.

1. _Client:_ sends `sendMessage` request to **ECB**
2. **_ECB:_** verifies `userToken`
3. **_ECB:_** verifies `threadToken`
4. **_ECB:_** If **ECB** is configured to block requests to `threads` where the `user` is not a member of that `thread`, **ECB** checks `thread` members first
5. **_ECB:_** send a `addThreadMessage` request to your server
6. _Your server:_ creates a new `threadMessages` , and stores it in your data store
7. _Your server:_ signs the `thread` with a `threadToken`
8. _Your server:_ returns the signed `thread` and new `threadMessage` to **ECB**
9. **_ECB:_** broadcasts the new message to all clients listening to this `thread`
10. **_ECB:_** returns the new `threadMessage` to the authoring client

## Next: [Data Structures <em> and models </em>](ECB-DATA-MODELS.md)
