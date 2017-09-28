---
title: NBA Email API Payload Document

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

search: true
---

# Introduction

This document only defines the payload format used by the Email API. 

It doesn't 

* Define how to implement the Email API
* Define how to send this payload to API backend
* Define how to implement the authentication

Also we use JavaScript language to show the payload format in this document

# Terminology

This document uses a number of terms to refer to the roles played in the API.

* **payload**

&nbsp;&nbsp;&nbsp;&nbsp;The message send to Email API backend

* **id**

&nbsp;&nbsp;&nbsp;&nbsp;The unique id to identify the payload send to Email API backend

* **transactionDate**

&nbsp;&nbsp;&nbsp;&nbsp;The UNIX epoch value in seconds for the time when we sent out the message

* **authtoken**

&nbsp;&nbsp;&nbsp;&nbsp;The authentication token used to authenticate the message call at server side

* **type**

&nbsp;&nbsp;&nbsp;&nbsp;Backend should use this field to determine the structure of data property

* **data**

&nbsp;&nbsp;&nbsp;&nbsp;The data contains in the payload which will be sent to Email API backend

* **optional**

&nbsp;&nbsp;&nbsp;&nbsp;If a data field is marked as optional. It means if the client side can't get the information, the whole filed will be omitted from the payload. For example, for purchase confirmation payload, the billing address for a customer is optional. So if the subscription doesn't require customer to input billing address, the the **billingaddress** field will be omitted from the payload.

# API Payload definition

## Subscription Self Cancellation

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "SUBSELFCANCEL",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test"
    },
    "subscription":{
      "name": "package name",
      "sku": "package sku",
      "resumable": false
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This payload will be sent to Email API backend when customer's subscription has been cancelled automatically based on system.

## Pruchase Confirmation

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "PURCHASECONFIRM",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test",
      "billingaddress": {
        "address1": "address1",
        "address2": "address2",
        "city": "city",
        "state": "state",
        "country": "country",
        "postalcode": "postalcode"
      },
      "payment": {
        "type": "creditcard",
        "method": {
          "cardnumber": "************5454",
          "expire": "12/2022"
        }
      },
      "order":{
        "subscription": {
          "name": "package name",
          "sku": "package sku",
          "freetrial": false,
          "freetrialbilldate": "2017-09-25T04:00:00Z",
        },
        "discount": 12.00,
        "subtotal": 12.00,
        "tax": 10.00,
        "total": 10.00
      }
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This payload will be sent to Email API backend when customer have succesfully bough a service at platform.

In the payload

* billingaddress is optional
* payment is optional
* payment => method is optional, and the data included in method will be different for each payment type. In the code we only show the method for creditcard payment method

## Refund

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "REFUND",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test",
      "payment": {
        "method": {
          "cardnumber": "************5454",
          "expire": "12/2022"
        }
      },
      "description": "refund reason"
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This message will be sent to Email API backend when customer get refund through platform.

In this payload 

* payment => method will include different field based on the refund method used. In the code, we only show the data field if refund method is credit card.

## Free Trial Expiring

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "FREETRIALEXPIRING",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test"
    },
    "subscription": {
      "name": "package name",
      "sku": "package sku",
      "daysremaining": 3,
      "nextbilldate": "2017-09-25T04:00:00Z",
      "price": 10.00,
      "tax": 10.00,
      "total": 20.00
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This message will be sent to Email API backend when customer's free trial subscription is going to be expired.

## Free Trial Billing Failure

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "FREETRIALBILLFAIL",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test"
    },
    "subscription": {
      "name": "package name",
      "sku": "package sku",
      "attempt": 3,
      "nextbilldate": "2017-09-25T04:00:00Z"
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This message will be sent to Email API backend when customer's rolloing free trial subscription billing failed.

## Free Trial Conversion

```javascript
const request = require("request")
const moment = require("moment")

//this is the payload sent to backend
const payload = {
  "id": "**************",
  "transactionDate": moment().unix(),
  "authtoken": "E76Aw1LTULNStu1h84Wl7t1AuUoY3fVepVJHdve2mwyRWEsHpV83",
  "type": "FREETRIALCONVERSION",
  "data": {
    "user": {
      "email": "test@me.com",
      "firstname": "test"
    },
    "subscription": {
      "name": "package name",
      "sku": "package sku",
      "price": 10.00,
      "tax": 10.00,
      "total": 20.00,
      "payment": {
        "method": {
          "cardnumber": "************5454",
          "expire": "12/2022"          
        }
      }
      "nextbilldate": "2017-09-25T04:00:00Z"
    }
  }
}

const options = {
  url: "http://example.com/api/email",
  method: "POST",
  headers: {
    "Content-Type": "application/json; charset=utf-8"
  },
  body: JSON.stringify(payload)
}

request(options, function (error, response, body){

})
```

This message will be sent to Email API backend when customer's rolling free trial subscription has been billed successfully and the free trial subscription is converted to a regular subscription.

In this payload

* payment => method will include different field based on the payment method used for the conversion. In the code, we only show the data field if payment method is credit card.