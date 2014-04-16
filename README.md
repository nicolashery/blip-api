# Blip API

A thin "proxy" API in front of the [Tidepool](http://tidepool.org/) platform used to simplify backend calls made by [Blip](https://github.com/tidepool-org/blip).

## Status

### GET /status

Check API health.

```javascript
// GET /status
```

```javascript
// 200 OK
```

## User

### POST /user

Sign up for a new user account. Also creates the user's profile information, as well as the user's "patients" group. Returns the user object (without the password), including an auth token to make further requests.

```javascript
// POST /user

{
  "username": "mary.smith@example.com",
  "password": "secret",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith"
  }
}
```

```javascript
// 201 Created

{
  "id": "b60222b6c7",
  "token": "QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4",
  "username": "mary.smith@example.com",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith"
  }
}
```

If an account already exists for that username:

```javascript
// 400 Bad Request

{
  "error": {
    "type": "AccountAlreadyExists",
    "message": "An account already exists for that username"
  }
}
```

### POST /user/login

Login with username and password. Returns the user object (without the password) and an auth token to make further requests.

```javascript
// POST /user/login

{
  "username": "mary.smith@example.com",
  "password": "secret"
}
```

```javascript
// 200 OK

{
  "id": "b60222b6c7",
  "token": "QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4",
  "username": "mary.smith@example.com",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith"
  }
}
```

If the credentials are incorrect:

```javascript
// 400 Bad Request

{
  "error": {
    "type": "InvalidCredentials",
    "message": "Wrong username or password"
  }
}
```

### GET /user/login

Refresh an auth token for a user: provide the auth token currently used, and get back a new one. Auth tokens have a lifetime of 1 hour.

```javascript
// GET /user/login
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

{
  "token": "ge8rlvc4h3C0epVi02D7HCj4K7ie3qrKqMez1CJqmIw"
}
```

If the token provided is wrong or expired:

```javascript
// 401 Unauthorized

{
  "error": {
    "type": "InvalidToken",
    "message": "Wrong username or password"
  }
}
```

### POST /user/logout

Terminate a user session and destroy its token.

```javascript
// POST /user/logout
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK
```

### GET /user/config

Get current's user's configuration information (ex: used for A/B testing).

```javascript
// GET /user/config
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

{
  "apiVersion": "v1"
}
```

### GET /user

Get current user's account and profile information.

```javascript
// GET /user
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

{
  "id": "b60222b6c7",
  "username": "mary.smith@example.com",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith"
  }
}
```

### PUT /user

Update current user's account and profile information.

```javascript
// PUT /user
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4

{
  "username": "new_email@example.com",
  "password": "new_secret",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith",
    "patient": {
      "birthday": "1987-03-08",
      "diagnosisDate": "1994-02-01",
      "aboutMe": "Loves swimming and fishing. Owns a bakery in San Francisco."
    }
  }
}
```

```javascript
// 200 OK

{
  "id": "b60222b6c7",
  "username": "new_email@example.com",
  "password": "new_secret",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith",
    "patient": {
      "birthday": "1987-03-08",
      "diagnosisDate": "1994-02-01",
      "aboutMe": "Loves swimming and fishing. Owns a bakery in San Francisco."
    }
  }
}
```

### GET /user/:id

Get a user's public information.

```javascript
// GET /user/b60222b6c7
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

{
  "id": "b60222b6c7",
  "profile": {
    "firstName": "Marie",
    "lastName": "Smith"
  }
}
```

## Shares

### POST /shares

Creates a "team" group for current user, to allow sharing health data with other users. Returns user's shared profile.

```javascript
// POST /shares
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 201 Created

{
  "id": "b60222b6c7",
  "profile": {
    "firstName": "Mary",
    "lastName": "Smith",
    "patient": {
      "birthday": "1987-03-08",
      "diagnosisDate": "1994-02-01",
      "aboutMe": "Loves swimming and fishing. Owns a bakery in San Francisco."
    }
  }
}
```

If the "share" has already been created:

```javascript
// 400 Bad Request

{
  "error": {
    "type": "ShareAlreadyExists",
    "message": "User is already sharing"
  }
}
```

### GET /shares

Get a list of people (short objects) sharing their data with current user.

```javascript
// GET /shares
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

[
  {
    "id": "cbdb0cc7f3",
    "profile": {
      "firstName": "Charles",
      "lastName": "West"
    }
  },
  {
    "id": "be8ec20d52",
    "profile": {
      "firstName": "Sarah",
      "lastName": "Carter"
    }
  },
  {
    "id": "103febca82",
    "profile": {
      "firstName": "Gregory",
      "lastName": "Jones"
    }
  }
]
```

### GET /shares/:id/about

Get shared profile information for a particular person.

```javascript
// GET /shares/cbdb0cc7f3/about
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

{
  "id": "cbdb0cc7f3",
  "profile": {
    "firstName": "Charles",
    "lastName": "West",
    "patient": {
      "birthday": "1982-06-07",
      "diagnosisDate": "2002-03-01",
      "aboutMe": "When I'm not working on designing beautiful websites, you can generally find me playing cricket."
    }
  }
}
```

If no user is found sharing at that `id`:

```javascript
// 404 Not Found

{
  "error": {
    "type": "ResourceNotFound",
    "message": "No share matches that id"
  }
}
```

### GET /shares/:id/stream

Get all shared health data for a particular person. This data is under the form of a stream of events (i.e. each data object is timestamped). Each datum also has a `type` attribute.

**Preprocessed stream**: By adding a `?preprocessed` query string to the URL fo the API call, some data preprocessing is done to make it ready for visualization in the app:

- Create `basal-rate-segment` objects (with `start` and `end` timestamps) from the series of `basal` objects
- Combine "dual" `bolus` objects ("normal" and "square" components) into a single `bolus` object
- Convert `wizard` objects into `bolus` objects (which is essentially a renaming/remapping of attributes)

Objects that were generated by this step have an `annotations` attribute, for example:

```javascript
{
  "type": "carbs",
  // ...
  "annotations": [
    {
      "code": "generated-from-wizard"
    }
  ]
}
```

The types to expect from this stream are:

- `basal-rate-segment`
- `bolus`
- `carbs`
- `cbg`
- `smbg`
- `settings`
- `message`


```javascript
// GET /shares/b60222b6c7/stream?preprocessed
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
// 200 OK

[
  {
    "type": "basal-rate-segment",
    "deviceTime": "2013-10-10T00:00:00",
    "shareId": "b60222b6c7",
    "id": "8eagr5sal9mlk2pmisf9h4qkklcdk25m",
    "start": "2013-10-10T00:00:00",
    "end": "2013-10-10T01:00:00",
    "deliveryType": "scheduled",
    "value": 0.8,
    // ...
  },
  {
    "type": "bolus",
    "deviceTime": "2013-10-10T17:49:55",
    "shareId": "b60222b6c7",
    "id": "n79iqo5gr1b6llviacrom8afrdb6o9rp",
    "subType": "normal",
    "value": 3.6,
    // ...
  },
  {
    "type": "carbs",
    "deviceTime": "2013-10-10T17:49:55",
    "shareId": "b60222b6c7",
    "id": "r3t6ollsnjpbsqjbttbc328je7sp5fvmcarbs",
    "value": 60,
    // ...
  },
  {
    "type": "cbg",
    "deviceTime": "2013-10-11T23:45:00",
    "shareId": "b60222b6c7",
    "id": "08hci32csrpk4a4onhiuhhs531iiur09",
    "value": 160,
    // ...
  },
  {
    "type": "smbg",
    "deviceTime": "2013-10-10T02:29:29",
    "shareId": "b60222b6c7",
    "id": "t1e5ej4lu7u24sf09i1689evl2pnhge0",
    "value": 150,
    // ...
  },
  {
    "type": "settings",
    "deviceTime": "2013-10-13T17:05:06",
    "shareId": "b60222b6c7",
    "id": "smio0obh7rdcgp5fuvgclaje3b24vui7",
    "basalSchedules": {
      "standard": [ /* ... */ ],
      "pattern a": [ /* ... */ ]
    },
    "carbRatio": [ /* ... */ ],
    "insulinSensitivity": [ /* ... */ ],
    "bgTarget": [ /* ... */ ],
    // ...
  },
  {
    "type": "message",
    "timestamp":"2014-04-15T13:08:37.596Z",
    "shareId": "b60222b6c7",
    "id":"534d2f55ccdf19e673000001",
    "parentMessage": null,
    "user": {
      "id": "b60222b6c7",
      "profile": {
        "firstName": "Mary",
        "lastName": "Smith",
      }
    }
    "messageText": "BG low, taking a juice box."
  }
]
```

**Raw stream**: By default (with no query string parameter), the API call returns "raw" data with no transformations.

The types to expect in this case are:

- `basal`
- `bolus`
- `wizard`
- `cbg`
- `smbg`
- `settings`
- `message`

**No cache**: Since this could be a relatively long-running query, results are cached for 1 hour. If a refresh is needed (ex: new data has been uploaded), add the `no-cache` query string parameter:

```javascript
// GET /shares/b60222b6c7/stream?preprocessed&no-cache
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

### GET /shares/:id/messages/:id/thread

Get the thread for a particular message: a list containing the "note" (parent message) and its "comments" (child messages).

```javascript
// GET /shares/b60222b6c7/messages/534d2f55ccdf19e673000001/thread
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4
```

```javascript
[
  {
    "timestamp": "2014-04-15T13:08:37.596Z",
    "id": "534d2f55ccdf19e673000001",
    "shareId": "b60222b6c7",
    "parentMessage": null,
    "user": {
      "id": "b60222b6c7",
      "profile": {
        "firstName": "Mary",
        "lastName": "Smith",
      }
    },
    "messageText": "BG low, taking a juice box."
  },
  {
    "timestamp": "2014-04-15T13:10:01.335Z",
    "id": "534d2f6dccdf19e673000002",
    "shareId": "b60222b6c7",
    "parentMessage": "534d2f55ccdf19e673000001",
    "user": {
      "id": "cbdb0cc7f3",
      "profile": {
        "firstName": "Charles",
        "lastName": "West"
      }
    },
    "messageText": "What did you have for lunch? Over-estimated your dual bolus maybe?"
  },
  {
    "timestamp": "2014-04-15T13:15:17.613Z",
    "id": "534d2f7dccdf19e673000003",
    "shareId": "b60222b6c7",
    "parentMessage": "534d2f55ccdf19e673000001",
    "user": {
      "id": "b60222b6c7",
      "profile": {
        "firstName": "Mary",
        "lastName": "Smith",
      }
    },
    "messageText": "I didn't finish my plate, but forgot to adjust for that :("
  }
]
```

### POST /shares/:id/messages/:id/comments

Reply to a message ("note") by creating a new comment. If `timestamp` is omitted, current time is used. The full message object is returned.

```javascript
// POST /shares/b60222b6c7/messages/534d2f55ccdf19e673000001/comments
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4

{
  "timestamp": "2014-04-15T13:15:17.613Z",
  "messageText": "I didn't finish my plate, but forgot to adjust for that :("
}
```

```javascript
// 201 Created

{
  "timestamp": "2014-04-15T13:15:17.613Z",
  "id": "534d2f7dccdf19e673000003",
  "shareId": "b60222b6c7",
  "parentMessage": "534d2f55ccdf19e673000001",
  "user": {
    "id": "b60222b6c7",
    "profile": {
      "firstName": "Mary",
      "lastName": "Smith",
    }
  },
  "messageText": "I didn't finish my plate, but forgot to adjust for that :("
}
```

### POST /shares/:id/messages

Create a new "note" for a user's shared data. If `timestamp` is omitted, current time is used. The full message object is returned.

```javascript
// POST /shares/b60222b6c7/messages/
// x-tidepool-session-token: QxQSHEjfmRu4vS2DPXe0ripPpnKaee1qn9Y0AiDBpH4

{
  "timestamp": "2014-04-15T13:08:37.596Z",
  "messageText": "BG low, taking a juice box."
}
```

```javascript
// 201 Created

{
  "timestamp": "2014-04-15T13:08:37.596Z",
  "id": "534d2f55ccdf19e673000001",
  "shareId": "b60222b6c7",
  "parentMessage": null,
  "user": {
    "id": "b60222b6c7",
    "profile": {
      "firstName": "Mary",
      "lastName": "Smith",
    }
  },
  "messageText": "BG low, taking a juice box."
}
```
