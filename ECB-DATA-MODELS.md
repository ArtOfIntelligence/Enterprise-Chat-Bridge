
## Enterprise Chat Bridge (ECB)

# Data Structures <em> and models </em>

This document explains **ECB's** data structures and models.

## Basic Models

**ECB** exchanges data in the following models:

| Model              | Description                                                                                                   |
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| `user`             | A chat system user.                                                                                           |
| `thread`           | A collection of messages bundled together represent a conversation, or a `thread`.                            |
| `threadMessage`    | A single message, which is the basic building block.                                                          |
| `pagingBase`       | **ECB** communicates arrays of data in a paginated manner, `pagingBase` is responsible for representing this. |
| `userJwtPayload`   | An encoded value, containing signed user identity information.                                                |
| `threadJwtPayload` | An encoded value, containing signed thread identity information.                                              |

## Additional Models

**ECB** exchanges data in the following models:

| Model               | Description |
| ------------------- | ----------- |
| `pagedThreads`      | A collection of threads wrapped in pagination context |
| `pagedContacts`     | A collection of users wrapped in pagination context |
| `threadAndMessages` | An object representing a single thread, and an array of messages |

## `user`

A chat system user.

### Exmaple

```json
{
  "userId": "usr-111",
  "displayName": "George Carlin",
  "data": {
    "isAgent": true,
    "profilePicUrl": ".../usr-111.jpg",
    "profilePicThumbUrl": ".../usr-111-thumb.jpg"
  }
}
```

### Schema

| Property      | Type              | Description                  | Required |
| ------------- | ----------------- | ---------------------------- | -------- |
| `userId`      | <kbd>string</kbd> | The user's unique identifier | Yes      |
| `displayName` | <kbd>string</kbd> | The user's displayName       | Yes      |
| `data`        | <kbd>any</kbd>    | Any additional user data     | No       |



## `thread`

A collection of messages bundled together represent a conversation, or a `thread`.

### Exmaple

```json
{
  "threadId": "thrd-999",
  "threadToken": "eyJ0eXAiOiJKV1QiLCJhbGciOi...",
  "members": [{"userId": "usr-111"}, ...],
  "lastMessageTime": 1593955934,
  "data": {
	  "ticketStatus": "Open"
  }
}
```
### Schema

| Property          | Type                    | Description                                                                                                                      | Required |
| ----------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `threadId`        | <kbd>string</kbd>       | The thread's unique identifier                                                                                                   | Yes      |
| `threadToken`     | <kbd>string</kbd>       | The thread's encoded and signed token                                                                                            | Yes      |
| `members`         | <kbd>user (array)</kbd> | All users who are members (participants) of the thread                                                                           | Yes      |
| `lastMessageTime` | <kbd>number\*</kbd>     | The date/time of the last message in the thread <p> \* Represented in [UNIX Epoch time](https://en.wikipedia.org/wiki/Unix_time) | Yes      |
| `data`            | <kbd>any</kbd>          | Any additional thread data                                                                                                       | No       |




## `threadMessage`

A single message, which is the basic building block.

### Exmaple

```json
{
  "messageId": "msg-55555",
  "fromUserId": "usr-111",
  "creationTime": 1593955934,
  "type": "text",
  "data": {
	  "messageText": "Hello..."
  }
}
```

### Schema

| Property          | Type                    | Description                                                                                                                      | Required |
| ----------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `messageId`        | <kbd>string</kbd>       | The message's unique identifier                                                                                                   | Yes      |
| `fromUserId`     | <kbd>string</kbd>       | The id of the authoring user                                                                                              | Yes      |
| `creationTime` | <kbd>number\*</kbd>     | The date/time when the message was sent <p> \* Represented in [UNIX Epoch time](https://en.wikipedia.org/wiki/Unix_time) | Yes      |
| `type`            | <kbd>string</kbd>|The type of the message**| Yes       |
| `data`            | <kbd>any</kbd>          | Any additional message data                                                                                                       | Yes       |

#### message `type`
Allows you to specify the type of the message. **ECB** reserves two values: `"text"` and `"typing"`.

##### message `type`: `"text"`
This indicates that your `message.data` property will contain a `messageText` string property with a plain text message. 

##### message `type`: `"typing"`
This indicates that your `message.data` property boolean value indicating whether the user is typing or not. 

##### message `type`:  `<anything else>` 
Allows you to send custom data inside the `data` property.


### `type`: `typing` Exmaple

```json
{
  "messageId": "--random-723--",
  "fromUserId": "usr-111",
  "creationTime": 1593955934,
  "type": "typing",
  "data": true
}
```

### `type`: `<anything else>` Exmaple

```json
{
  "messageId": "msg-55555",
  "fromUserId": "usr-111",
  "creationTime": 1593955934,
  "type": "document",
  "data": {
	  "title": "Device Setup Guide 1",
	  "thimbnailUrl": ".../device-setup-guide-01.jpg",
	  "downloadUrl": ".../device-setup-guide-01.pdf"
  }
}
```

## `paginBase`

**ECB**  communicates arrays of data in a paginated manner,  `pagingBase`  is responsible for representing this.

### Exmaple

```json
{
  "pageStartIndex": 0,
  "pageSize": 10
}
```

### Schema

| Property      | Type              | Description                  | Required |
| ------------- | ----------------- | ---------------------------- | -------- |
| `pageStartIndex`      | <kbd>number</kbd> |The pagination start index <br> *Minimum value: 0*| Yes      |
| `pageSize` | <kbd>number</kbd> | The maximum number of items per page <br> *Minimum value: 1*| Yes|
 


## `userJwtPayload`

A <kbd>string</kbd> containing a signed and encoded `user` object.

### Exmaple

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1....
```

## `threadJwtPayload`

A <kbd>string</kbd> containing a signed and encoded subset of the `thread` object.

### Exmaple

```
naW5lRGVtbzFTZXJ2ZXIiLCJzdWIiOm51bGwsImF1ZCI...
```

### Schema
When decoded, the token contains the following:

| Property    | Type                      | Description                                | Required |
| ----------- | ------------------------- | ------------------------------------------ | -------- |
| `threadId`  | <kbd>string</kbd>         | The thread's unique identifier             | Yes      |
| `memberIds` | <kbd>string (array)</kbd> | The ids of thread's members (participants) | Yes      |



## `pagedThreads` <small>(Additional Model)</small>

A collection of threads wrapped in pagination context.

This is the response the client receives when it requests the user's list of threads.

### Exmaple

```json
{
  "pageStartIndex": 0,
  "pageSize": 12,
  "items": [{"threadId":"thrd-999"}, ...],
  "previousPage": null,
  "nextPage": {
    "pageStartIndex": 12,
    "pageSize": 12
  } 
}
```

### Schema


| Property      | Type              | Description                  | Required |
| ------------- | ----------------- | ---------------------------- | -------- |
| `pageStartIndex` | <kbd>number</kbd> |The pagination start index of the current result set| Yes      |
| `pageSize` | <kbd>number</kbd> | The number of items in the current result set | Yes|
| `items` | <kbd>thread (array)</kbd> | The threads of the current result set | Yes |
| `previousPage` |<kbd>pagingBase</kbd>| Pagination settings to request previous page <br> `null` if there is no previous page|No|
| `nextPage` |<kbd>pagingBase</kbd>| Pagination settings to request next page <br> `null` if there is no next page|No|
 

## `pagedContacts` <small>(Additional Model)</small>

A collection of users wrapped in pagination context.

This is the response the client receives when it requests the list of contacts it is allowed to "contact" (start a mew thread with).

### Exmaple

```json
{
  "pageStartIndex": 0,
  "pageSize": 12,
  "items": [{"userId":"thrd-111"}, ...],
  "previousPage": null,
  "nextPage": {
    "pageStartIndex": 12,
    "pageSize": 12
  } 
}
```

### Schema


| Property      | Type              | Description                  | Required |
| ------------- | ----------------- | ---------------------------- | -------- |
| `pageStartIndex` | <kbd>number</kbd> |The pagination start index of the current result set| Yes      |
| `pageSize` | <kbd>number</kbd> | The number of items in the current result set | Yes|
| `items` | <kbd>user (array)</kbd> | The users of the current result set | Yes |
| `previousPage` |<kbd>pagingBase</kbd>| Pagination settings to request previous page <br> `null` if there is no previous page|No|
| `nextPage` |<kbd>pagingBase</kbd>| Pagination settings to request next page <br> `null` if there is no next page|No|
 
 

## `threadAndMessages` <small>(Additional Model)</small>

 An object representing a single thread, and an array of messages.
 
 This is the object the client receives as response when creating a new thread or adding a message to an existing thread. 
 
### Exmaple

```json
{
  "thread": {"threadId": "thrd-999", ...},
  "messages": [{"messageId":"msg-55555"}, ...]
}
```

### Schema


| Property      | Type              | Description                  | Required |
| ------------- | ----------------- | ---------------------------- | -------- |
| `thread` | <kbd>thread</kbd> | The thread | Yes |
| `messages` | <kbd>message (array)</kbd> | The messages of the thread| Yes |
