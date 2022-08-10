# EasyWebShop Rest interface
paytrailnotify\
history\
status

## Protocol
All queries are POST type and use JSON data format.
In this document the path of the easy API is first and
after that JSON fields are described in tables.
See examples at the end of the document.


### Table of Contents
[Base url](#base-url)\
[Response](#response)\
[Shop id](#shop-id)\
[Locale](#locale)\
[User accounts](#user-accounts)\
[Endpoints](#endpoints)\
[/customers](#/customers)\
[/events](#/events)\
[/eventslotdays](#/eventslotdays)\
[/eventslots](#/eventslots)\
[/products](#/products)\
[/login](#/login)\
[/logout](#/logout)\
[/passwordhash](#/passwordhash)\
[/register](#/register)\
[/passwordset](#/passwordset)\
[/registerhash](#/registerhash)\
[/registerset](#/registerset)\
[/cartupdate](#/cartupdate)\
[/commit](#/commit)\
[/committest](#/committest)\
[/paid](#/paid)\
[/paidtest](#/paidtest)\
[/cardnumber](#/cardnumber)\
[/sale](#/sale)\
[Examples](#examples)


## Base url
`https://example.com/v1`


## Response

Server response always includes code and data fields. Code -field indicates the server’s response code, where 0 is success and any other value error. Data -field includes data that is described in this document.

```javascript
/**
 * @template T
 * @typedef {Object} Response1
 * @property {number} code - 0 for success. non-zero for error.
 * @property {T[]}    data - includes data that is described in this document.
 */

/** @type {Response1<any>} */
const response = {
  "code": 0,
  "data": []
}
```

## Shop id

For Snowcard webshop, 'sc' should be included in POST's params.

```javascript
/**
 * @typedef {'sc'|number} Shop
 */
```

## Locale

Supported locales are: fi, se, en.\
Default: fi

```javascript
/**
 * @typedef {'fi'|'se'|'en'} Language
 */
```

## User accounts

New user registration process:
1. Register a new user (with email, no token) and get a token
2. Get preregister hash with token (this should be sent to user’s email)
3. Set preregister hash and get a token (user calls this from the email)
User registration values can be changed using register call and a token.


## Endpoints

```javascript
/**
 * All endpoints
 *
 * @typedef {'/customers'|'/products'|'/events'|'/eventslotdays'|'/eventslots'|'/cardnumber'} EndpointCommon
 * @typedef {'/login'|'/logout'|'/register'|'/registerhash'|'/registerset'|'/passwordhash'|'/passwordset'} EndpointUser
 * @typedef {'/commit'|'/paid'|'/cartupdate'|'/sale'} EndpointSale
 * @typedef {'/committest'|'/paidtest'} EndpointTest
 *
 * @typedef {EndpointCommon|EndpointUser|EndpointSale|EndpointTest} Endpoint
 */
```

## /customers

```javascript
/**
 * Params passed to /customers
 *
 * @typedef {Object} CustomersParams
 * @property {Shop} shopId
 * @property {number} [customer] - optional customer id
 */

/**
 * /customers returns a Response1<Customer>
 *
 * @typedef {Object} Customer
 * @property {string} id
 * @property {string} name
 * @property {string[]} address
 * @property {string[]} images   - customer image links
 * @property {number} cardType   - card type, 0 for snowcard
 */

/** @type {CustomersParams} */
const params = {
  shopId: 'sc',
  customer: '62',
}

const init = {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(params),
}

fetch(BASE_URL + '/customers', init)
  .then(res => res.json())
  .then(res => {

    /** @type {Response1<Customer>} */
    const response = res

    if (response.code !== 0) {
      console.log('/customers error code', response)
      throw response
    }

    for (const customer of response.data) {
      console.log(customer)
    }
  })
```

## /events

```javascript
/**
 * Params passed to /events
 *
 * @typedef {Object} EventsParams
 * @property {Shop} shopId
 * @property {number} customer - customer id
 * @property {number} dateTS   - events date, unix timestamp
 * @property {Language} [language='fi']
 */

/**
 * Event type may be not set or following: @FIXME may be not set, so optional?
 *
 * product_sale - plain product sale event
 * time_slot    - requires time slot for ticket purchase
 *
 * @typedef {'product_sale'|'time_slot'} EventType
 */

/**
 * /events returns a Response1<Event1>
 *
 * @typedef {Object} Event1
 * @property {string} id
 * @property {string} name
 * @property {number} start - start time, unix timestamp
 * @property {number} stop  - stop time, unix timestamp
 * @property {EventType} [type] @FIXME may be not set, so optional?
 */

/** @type {EventsParams} */
const params = {
  shopId: 'sc',
  customer: 62,
  dateTS: Date.now(),
  language: 'fi'
}

const init = {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(params),
}

fetch(BASE_URL + '/events', init)
  .then(res => res.json())
  .then(res => {

    /** @type {Response1<Event1>} */
    const response = res

    if (response.code !== 0) {
      console.log('/events error code', response)
      throw response
    }

    for (const event of response.data) {
      console.log(event)
    }
  })
```
