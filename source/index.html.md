---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - json
  - ruby

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

The Insight API allows an approved third party to integrate with the core Insight system for performing various Card functions such as loading and unloading, and general account maintenance. In addition to Card functions, it also provides access to Store and User maintenance for the Agent. 

# Authentication

All the API methods require authentication parameters to be passed as part of the URL string on the HTTP request. The API user will be authenticated using the login, password, and partner_code parameters. This information will be provided by Insight. See Default Failure Responses for example of the Access Denied failure response. In addition to the login, password and partner_code, a valid request_user (similar to user_identifier) and request_store (similar to store_identifier) must be passed as a parameter for each request to track the user that performed the action and the store at which the action was performed. Finally, it is recommended to pass a request_guid as a part of the URL string so that you can retrieve the details of any previous request. This is useful in the case of a network connection issue or timeout where you may not have received a response to the API request, although the request may have been processed successfully at Insight. 

# Versions

Periodically the API methods will need to be updated to accept new data as part of a request or return new data as part of the response. If a breaking change is introduced then a new version of the method will be created, and a notice will be sent to all consumers of the API that a new version of a method is available once it has passed QA and is deployed into production. The API consumer will have adequate time to test the new version in the Insight UAT environment. Once the API consumer is ready to begin calling the new version of a method in production, the “version” parameter specified when calling the method can simply be changed to the new version for that particular method. The API consumer may revert back to the previous version of the method by changing the “version” parameter specified when calling the method. 

A timeline for switching to a new version of the API will be determined by Insight and communicated to the API consumer. Once the published sunset date for a given API method has been reached, access to the method will be removed from production. 

# Method Access Control

Although all of the methods supported by the API are detailed in this document, only a subset might be available. Each method must be specifically white listed for the API user before access to that method is allowed. A form will be provided by Insight to the Partner that will be used to request authorization to any API methods which are needed by the Partner. Any attempt to access a method that is not allowed will return a response code of -3. 

# Guids

Globally unique identifiers are used throughout the API when data elements are returned and may also be used to identify a specific API request. It is strongly recommended that you store these GUIDs in your system to be used for later retrieval of data and/or previous API request details. GUIDs are considered non-sensitive information; therefore, they can be stored unencrypted and sent through email when debugging issues. All GUIDs should follow the standard GUID format of 550e8400-e29b-41d4-a716-446655440000.

See http://en.wikipedia.org/wiki/Globally_unique_identifier for more information on GUIDs.

#Formats

All API methods are designed to accept either a JSON request body or an XML request body. You must set the Content-Type header to either application/json or application/xml depending on the request body you are passing. In addition, the method response may be returned in either JSON or XML. The response format is set by appending “.json” or “.xml” after the method name in the request URL. 


# Response Format

```JSON
{
  "request_guid":<REQUEST GUID>,
  "success":<SUCCESS>,
  "messages":[{"message":{"code":<CODE>, "description":<DESCRIPTION>}}],
  "data":{
    <DATA>  
  },
  "request_received":<REQUEST RECEIVED>,
  "response_sent":<RESPONSE SENT>,
  "version":<VERSION>
}
```

All API methods will return with a standardized response that includes the following details. 

Field Name | Description
--------- | -----------
request_guid | The unique identifier provided by the consumer when the request was submitted
success | A boolean true or false depending on whether or not the action was successful 
messages | An array of one or more messages describing the result of the request  
code | A unique code to provide a more detailed result of the request 
description | A detailed description of the result of the request 
data | Elements of the optional data node will vary depending on the response 
request_received | The date and time the request was received 
response_sent | The date and time the response was sent
version| The version of the API method that was called 
 
#Standard Response Codes

```json

# -2: Access Denied
{
  "response": {   
    "request_guid": "3b0504e6-6d29-47ff-a65e-da6c87956b42",   
    "success": false,   
    "messages": [{"message":{"code": -2,    
                             "description": " Access Denied: Invalid login, password, or partner_code." }}],   
    "data": {},   
    "request_received": "2012-07-14T15:55:59-04:00",   
    "response_sent": "2012-07-14T15:55:59-04:00",   
    "version": "1"
  }
}

# -3: Method Not Allowed 
{
  "response": {   
    "request_guid": "3b0504e6-6d29-47ff-a65e-da6c87956b42",   
    "success": false,   
    "messages": [{"message":{"code": -3,    
                             "description": "User is not allowed to access this method." }}],   
    "data": {},   
    "request_received": "2012-07-14T15:55:59-04:00",   
    "response_sent": "2012-07-14T15:55:59-04:00",   
    "version": "1"
  }
}

# 10: Invalid Parameters
{
  "response":{
    "request_guid":"57c26fa8-648f-4511-81c8-b5de65dd1d05",
    "success":false,
    "messages":[
      {
        "message":{
          "code":10,
          "description":"One or more of your parameters is invalid"
        }
      }
    ],
    "data":{
      "validation_errors":[
        {
          "error_message":"can't be blank",
          "field_with_error":"identifier"
        }
      ]
    },
    "request_received":"2012-07-15T20:31:05-04:00",
    "response_sent":"2012-07-15T20:31:05-04:00",
    "version":"1"
  }
}
```


The following response codes are used by all the methods in the API. Each method can have additional response codes that will be detailed in the method documentation below. 

Code | Description
---- | -----------
-3	| Access to this method is not allowed for your user
-2	| Invalid Login, Password or Partner Code
-1	| Unknown System Failure
10	| Invalid Parameters
20	| An error occurred at the processor
201	| The user with user_identifier <USER_IDENTIFIER> could not be found. 
204	| A Store with store_identifier <STORE IDENTIFIER> belonging to the Agent <AGENT NAME> could not be found or is disabled.

# Adjustments
## Create Adjustment
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/adjustments.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "amount":"10.00"
}
```

> Response

```json
{
  "response":{
    "request_guid":"afb43d7a-0737-40ec-a7f0-742e278328c5",
    "success":true,
    "messages":[],
    "data":{
      "amount":"5.00",
      "old_balance":"186.60",
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642",
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      },
      "transaction_identifier":"Ve8mEK6jUKulAMy7xYGu",
      "fee_amount":"0.00",
      "new_balance":"191.60"
    },
    "request_received":"2012-07-15T18:57:22-04:00",
    "response_sent":"2012-07-15T18:57:24-04:00",
    "version":"1"
  }
}
```

Creates an adjustment on the specified card account.  

### HTTPS Request
`/api/card_accounts/:card_account_identifier/adjustments`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
amount | Y | Currency in format 0.00 | Amount of the adjustment
transaction_type | N | Valid transaction type | A valid transaction type to classify the adjustment

### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME>

## Reverse Adjustment
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/adjustments/kjh02dfc-4567-9874-82ed-b4b60ab6bba3.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"006ba472-2a4f-448d-9fe7-dd2ba3180dc5",
    "success":true,
    "messages":[],
    "data":{
      "old_balance":"196.60",
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642",
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      },
      "transaction_identifier":"Ve8mEK6jUKulAMy7xYGu",
      "new_balance":"191.60",
      "fee_reversal_amount":"0.00"
    },
    "request_received":"2012-07-15T19:04:28-04:00",
    "response_sent":"2012-07-15T19:04:29-04:00",
    "version":"1"
  }
}
```

Reverses a previous adjustment on a card account.

### HTTPS Request
`/api/card_accounts/:card_account_identifier/adjustments/:transaction_identifier(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
206 |	A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME>	

# Alerts
## Get Alerts
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/alerts.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"6a612131-3a86-49b8-9e37-08fcc37158ec",
    "success":true,
    "messages":[],
    "data":{
      "alerts":[
        {
          "alert":{
          "name":"BPCQ",
          "sms":false,
          "email":false
        }},
        {
          "alert":{
          "name":"BNEG",
          "sms":false,
          "email":false
        }},
        {
          "alert":{
          "name":"BPAY",
          "sms":false,
          "email":false
        }},
        {
          "alert":{
          "name":"FACH",
          "sms":false,
          "email":false
        }},
        {
          "alert":{
          "name":"BBAL",
          "sms":false,
          "email":false
        }}
      ]
    },
    "request_received":"2012-07-15T01:53:49-04:00",
    "response_sent":"2012-07-15T01:53:49-04:00",
    "version":"1"
  }
}
```

Gets the current alerts for the card account.

### HTTPS Request
`GET /api/card_accounts/:card_account_identifier/alerts(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME> 
530	| Alerts for the card account could not be retrieved.


## Set Alerts
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/alerts.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "alerts":[
  {
    "alert":{
      "name":"BADJ",
      "sms":false,
      "email":false
    }
  },
  {
    "alert":{
      "name":"BFID",
      "sms":false,
      "email":false
    }
  }]
}

```

> Response

```json
{
  "response":{
    "request_guid":"aaa02dfc-4567-9874-82ed-b4b60ab6bab8",
    "success":true,
    "messages":[],
    "data":{},
    "request_received":"2012-07-15T02:56:07-04:00",
    "response_sent":"2012-07-15T02:56:07-04:00",
    "version":"1"
  }
}
```

Enables or disables alerts on the card account. Only alerts whose status needs to be updated should be included.  

### HTTPS Request
`PUT /api/card_accounts/:card_account_identifier/alerts(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------

# API Requests
## Get API Request
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/api_requests/aaa02dfc-4567-9874-82ed-b4b60ab6bab8.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response": {
    "request_guid": "8431b128-d57c-4b65-ae4a-892c3ad76987",
    "success": true,
    "messages": [],
    "data": {
      "original_response": "{\"response\":{\"request_guid\":\"444ca9f2-2a59-46a5-9ad6-e4bd8938bde3\",\"success\":true,\"messages\":[],\"data\":{\"stores\":[{\"store\":{\"name\":\"Store #1\",\"guid\":\"0985fb1f-2a4a-4d2e-92e1-936974a786cc\"}},{\"store\":{\"name\":\"Galileo\",\"guid\":\"3ca1caa7-1dc9-4344-9674-6d81561b4050\"}}]},\"request_received\":\"2012-07-14T23:06:22-04:00\",\"response_sent\":\"2012-07-14T23:06:22-04:00\",\"version\":\"1\"}}"
    },
    "request_received": "2012-07-14T23:06:53-04:00",
    "response_sent": "2012-07-14T23:06:53-04:00",
    "version": "1"
  }
}

```

Retrieves a previous API request and returns the original response for that request.


### HTTPS Request
`GET /api/api_requests/:original_request_guid(.format)`


### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
213	| An api request with guid <ORIGINAL REQUEST GUID> cannot  be found for the api user.  


# Available Methods
## Get Available Methods
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/available_methods.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response": {
    "request_guid": "05bfcc8c-14ba-4166-9f9d-eb2817e924a0",
    "success": true,
    "messages": [],
    "data": {
      "available_methods": [ 
        {
          "available_method": {
            "http_method": "get",
            "url": "/api/users(.:format)",
            "version": "1"
          }
        },
        {
          "available_method": {
            "http_method": "post",
            "url": "/api/users(.:format)",
            "version": "1"
          }
        },
        {
          "available_method": {
            "http_method": "post",
            "url": "/api/card_accounts/:card_account_identifier(.:format)",
            "version": "1"
          }
        },
        {
          "available_method": {
            "http_method": "get",
            "url": "/api/card_accounts(.:format)",
            "version": "1"
          }
        },
        {
          "available_method": {
            "http_method": "post",
            "url": "/api/card_accounts(.:format)",
            "version": "1"
          }
        },
        {
          "available_method": {
            "http_method": "get",
            "url": "/api/stores(.:format)",
            "version": "1"
          }
        }
      ]
    },
    "request_received": "2012-07-14T19:29:50-04:00",
    "response_sent": "2012-07-14T19:29:50-04:00",
    "version": "1"
  }
}
```

Returns a list of all versions of the API methods that the current API user is authorized to use. If additional API methods are needed, please contact Insight Partner Support to have the method white listed for your API user. 

### HTTPS Request
`GET /api/available_methods(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
540 |	The api user <LOGIN> does not have access to any methods. 
  
## Card Accounts
## Get Card Account
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"e4cbbe12-b794-4052-83c3-f2a049c1333e",
    "success":true,
    "messages":[],
    "data":{
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642",
        "product_id":"1234",
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      }
    },
    "request_received":"2012-07-15T17:25:58-04:00",
    "response_sent":"2012-07-15T17:25:58-04:00",
    "version":"1"
  }
}
```

Gets attributes of the card account, optionally including all the cards associated with the card account.

### HTTPS Request
`GET /api/card_accounts/:card_account_identifier(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
206 |	A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME> 
  
## Get Card Account Guid
> Example Request Query
> <br>
>

> Request

```json
{
  "identifier":"113612345678"
}
```

> Response

```json
{
  "response":{
    "request_guid":"ae0ee6d8-529e-4589-8d73-b0566ca7359a",
    "success":true,
    "messages":[],
    "data": {
      "card_account": {
        "created_at":"07/14/2012",
        "customer_number":"113612345678",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642"
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      }
    },
    "request_received":"2012-07-14T23:24:37-04:00",
    "response_sent":"2012-07-14T23:24:38-04:00",
    "version":"1"
  }
}
```

Finds and returns a card account GUID based on a Customer Number or PAN. 

### HTTPS Request
`POST /api/card_account_guids(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
206 |	A Card Account with card_account_identifier <CARD_ACCOUNT_IDENTIFIER> could not be found.

# Cards
## Create Card
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/cards.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "amount": "10.00",
  "card_product_id": "1214",
  "card_number": "4368790112345678",
  "fee_plan": "0",
  "person": {
    "first_name":"User",
    "last_name":"Test",
    "middle_initial":null,
    "address1":"600 Beacon Parkway",
    "address2":null,
    "city":"Birmingham",
    "postal_code":"35242",
    "postal_region":"AL",
    "date_of_birth":"01/02/1990",
    "locale":"en",
    "id1_type":"0",
    "id1_number":"123-45-4568",
    "id1_expires_on":"",
    "id1_issued_on":"",
    "id1_issued_location":null,
    "id2_type":"4",
    "id2_number":"12341234",
    "id2_expires_on":"01/03/2015",
    "id2_issued_on":"01/03/2001",
    "id2_issued_location":"US",
    "phone_number":"",
    "work_number":"",
    "mobile_number":"1231234567",
    "should_receive_sms_messages":false,
    "payroll_phone_number":"123-123-4567",
    "security_question_1_answer":"test",
    "security_question_1":"What is the answer?",
    "pay_day_of_week":null,
    "pay_week_of_month":null,
    "initial_next_pay_date":"11/12/2012",
    "employer_name_or_source_of_income":"Insight",
    "pay_frequency":null,
    "is_employee":false,
    "direct_deposit":false,
    "net_pay_each_period":null,
    "email_address":"test@example.com"
  }
}
```

> Response

```json
{
  "response": {
    "request_guid": "def05aef-7d22-41a6-828c-aa139fe29b22",
    "success": true,
    "messages": [],
    "data":{
      "cip": {
        "status": "Pass",
        "messages": {
          "message": {
            "code": "Pass 01",
            "description": "SSN Matches Name and Address"
          }
        }
      },
      "person":{
        "use_secondary_mobile_number_for_sms":false,
        "city":"Birmingham",
        "address1":"600 Beacon Parkway",
        "fax_number":null,
        "id2_issued_on":"01/03/2001",
        "created_at":"07/14/2012",
        "address2":null,
        "payroll_phone_number":"123-123-4567",
        "id1_issued_location":null,
        "guid":"bf76225c-bd19-4a73-a691-38f513579dc0",
        "updated_at":"07/14/2012",
        "pay_week_of_month":null,
        "postal_code":"35242",
        "id1_issued_on":"",
        "pay_day_of_week":null,
        "pay_date_context_date":null,
        "initial_next_pay_date":"11/12/2012",
        "id2_type":"4",
        "security_question_1":"What is the answer?",
        "is_employee":false,
        "employer_name_or_source_of_income":"Insight",
        "work_number":"",
        "id2_issued_location":"US",
        "id1_type":"0",
        "mobile_number":"1231234567",
        "phone_number":"",
        "id2_number":"***41234",
        "pay_frequency":null,
        "secondary_mobile_number":null,
        "last_name":"Test",
        "card_accounts":[
          {
            "card_account":{
              "created_at":"07/14/2012",
              "customer_number":"756536607908",
              "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
              "routing_number":"06120642",
              "cards":[
                {
                  "card":{
                    "created_at":"07/14/2012",
                    "last_four_digits":"0000",
                    "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
                    "status":"N",
                    "balance":"10.00"
                  }
                }
              ]
            }
          }
        ],
        "locale":"en",
        "security_question_1_answer":"test",
        "direct_deposit":false,
        "net_pay_each_period":null,
        "email_address":"test@example.com",
        "date_of_birth":"01/02/1990",
        "middle_initial":null,
        "id2_expires_on":"01/03/2015",
        "id1_number":"******-4568",
        "should_receive_sms_messages":false,
        "postal_region":"AL",
        "id1_expires_on":"",
        "first_name":"User"
      }
    },
    "request_received": "2012-07-14T15:24:02-04:00",
    "response_sent": "2012-07-14T15:24:08-04:00",
    "version": "1"
  }
}
```

Creates a new Insight Instant Issue or Personalized Visa Card using the provided customer information. A new person, card account, and card will be created. 

### HTTPS Request
`POST /api/cards(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
amount	                | N	| Currency value in the format 0.00	| The amount of funds to initially load to the card. Defaults to 0.00
transaction_type	      | N	| Valid transaction type	| A valid transaction type to classify the load.
card_product_id	        | ?	| Valid card product id. Required for personalized card (when not passing card_number).	| Identifies the product type for the card. 
card_number	            | ?	| Valid 16 Digit Card Number. Required for instant issue card. 	| Card number for the instant issue card.
fee_plan	              | N	| Valid fee plan. See Fee Plans table.	| Default fee plan is monthly.
person[first_name]      | Y	| Maximum 20 characters	| Cardholder’s first name, see note 2.
person[last_name]	      | Y	| Maximum 20 characters	| Cardholder’s last name, see note 2.
person[middle_initial]	| N	| Maximum 1 character	| Cardholder’s middle initial, see note 2.
person[address1]	      | Y	| Maximum 26 characters	| Cardholder’s address line 1.
person[address2]	      | N	| Maximum 26 characters	| Cardholder’s address line 2.
person[city]	          | Y	| Maximum 30 characters	| Cardholder’s city.
person[postal_code]	    | Y	| Valid zip code or Zip+4 with dashes. 	| Cardholder’s postal code.
person[postal_region]	  | Y	| Valid 2-letter state abbreviation	| Cardholder’s postal region.
person[date_of_birth]	  | Y	| Date format: MM/DD/YYYY, Must be at least 18 years of age |	Cardholder’s date of birth.
person[locale]	        | N	| Valid Locale. See locales table.	| Locale (language) of the person. Defaults to English (en).
person[id1_type]	      | Y	| "See Identification Requirements, Must be different than id2_type"	| Type of id provided
person[id1_number]	    | Y	| Valid number depending on type | Identification number such as the SSN.
person[id1_expires_on]	| ?	| Date in format MM/DD/YYYY, See Identification Requirements |	Date the identification expires.
person[id1_issued_on]	  | ?	| Date in format MM/DD/YYYY, See Identification Requirements |	Date the identification was issued.
person[id1_issued_location]	| ? |	See Identification Requirements |	Location at which the id was issued, see note 1.
person[id2_type]	      | ? |	See Identification Requirements, Must be different than id1_type	| Type of alternate form of identification. 
person[id2_number]	    | ? |	Valid number depending on type	| Alternate identification number.
person[id2_expires_on]	| ? |	Date in format MM/DD/YYYY, See Identification Requirements |	Date id2 expires on, see note 1.
person[id2_issued_on]	  | ? |	Date in format MM/DD/YYYY, See Identification Requirements |	Date id2 was issued on, see note 1.
person[id2_issued_location]	?	See Identification Requirements	| Location at which the id2 was issued, see note 1.
person[phone_number]    | ? |	10 digits, no dashes, Required if mobile_number is blank | Cardholder’s phone number.
person[work_number]	    | N	| 10 digits, no dashes	| Cardholder’s work number.
person[mobile_number]	  | ?	| 10 digits, no dashes, Required if phone_number is blank or should_receive_sms_messages is true|	Cardholder’s mobile number.
person[should_receive_sms_messages]	      | N |	true or false	| Defaults to false. Set to true if the person should receive sms alerts. 
person[payroll_phone_number]	            | N |	Suggested if employer_name_or source_of_income is provided	| Phone number of the cardholder’s payroll provider.
person[security_question_1]	              | N |	Maximum 50 characters	| Security question to ask the cardholder for account verification
person[security_question_1_answer]	      | ? |	Required if security_question_1 is present |	Answer to the security question.
person[pay_day_of_week]	                  | ? |	Required if pay frequency is monthly | Valid day of week integer, see Days of Week table. 	Day of the week on which the person is paid.
person[pay_week_of_month]	                | ? |	Required if pay frequency is monthly | Valid values are 1,2,3, or 4.	Week of the month on which the person is paid.
person[initial_next_pay_date]	            | N |	Date in format MM/DD/YYYY	| Date the cardholder will be paid next.
person[employer_name_or_source_of_income]	| N |	Should not be blank	| Name of employer or income source.
person[is_employee]	                      | N |	true or false	| Defaults to false. Should be set to true if the person is an employee of the agent.
person[direct_deposit]	                  | N |	true or false	| Defaults to false. Should be set to true if the person is enrolling in direct deposit.
person[net_pay_each_period]	              | N |	Currency value in the format 0.00	| The amount the person is typically paid on each pay period.
person[email_address]	                    | Y |	Valid email address	| The email address of the person.


<aside class="notice">
  Note 1: Expires On, Issued On, and Location are conditionally required based on the id type that is selected. If 1 or 2 is selected for the id_type or id2_type then these fields are required.
</aside>
<aside class="notice">
Note 2: First Name, Last Name, and Middle Initial cannot combine to be more than 28 characters. If a middle initial is used it will add 3 characters to the total count because of spaces on each side. This is a limitation related to embossing the name on the card.
</aside>

### Response Codes
Code | Description
---- | -----------
420	| A card has been issued but funds could not be loaded.
421	| A card has been issued but the customer failed CIP. CIP will need to be re-run once the customer information has been updated. 
422	| A card could not be issued

## Update Card
> Example Request Query
> <br>
 `https://phase2.insightcards.com/api/cards/2bae3443-21dc-42f6-b83f-d140346f361e.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "status":"L",
  "new_account_number":"4368790187654321"
}
```

> Response

```json
{
  "response":{
    "request_guid":"bbbac7f8-984d-42e6-a42c-f96b908953cb",
    "success":true,
    "messages":[],
    "data":{
      "card":{
        "created_at":"07/14/2012",
        "last_four_digits":"1234",
        "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
        "status":"L",
        "balance":"743.82"
      }
    },
    "request_received":"2012-07-15T18:59:01-04:00",
    "response_sent":"2012-07-15T18:59:03-04:00",
    "version":"1"
  }
}


```
Updates the status of an existing card.

### HTTPS Request
`/api/cards/:card_identifier(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
status | N | Valid card status from the Card Status table | The new status for the card.
card_product_id | N |	Valid card product id |	Identifies the product type for the card.
new_account_number	| ? | Valid 16-digit card number, Required if changing status to Lost or Stolen and replacing with an instant issue card | New instant issue card number to replace lost or stolen card.

### Response Codes
Code | Description
---- | -----------
205	| A Card with card_identifier <CARD IDENTIFIER> could not be found


## Get Card Guid
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/cards.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "identifier":"4368790112345678"
}
```

> Response

```json
{
  "response":{
    "request_guid":"74482df7-b32d-4656-bffc-6542c2b06ce6",
    "success":true,
    "messages":[],
    "data":{
      "card":{
        "created_at":"07/14/2012",
        "last_four_digits":"0000",
        "guid":"c1d32685-7063-4f22-8649-89bc1274bab4",
        "status":"N",
        "balance":"0.00"
      }
    },
    "request_received":"2012-07-14T23:23:32-04:00",
    "response_sent":"2012-07-14T23:23:32-04:00",
    "version":"1"
  }
}

```

Finds and returns a card GUID based on a Customer Number or PAN. 

### HTTPS Request

`POST /api/card_guids(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
identifier | Y | Valid 12-digit Customer Number or 16-digit Card Number	| Refer to list of Card Identifiers or Card Account Identifiers

### Response Codes
Code | Description
---- | -----------
520 | There are no records that match your search criteria

# Convenience Checks
## Create Convenience Check 
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/convenience_checks.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "identifier":"113612345678",
  "check_number":"1234",
  "amount":"100.00",
  "payee":"John Doe"
}
```

> Response

```json
{
  "response":{
    "request_guid":"ae0ee6d8-529e-4589-8d73-b0566ca7359a",
    "success":true,
    "messages":[],
    "data": {
      "convenience_check": {
        "authorized_date":"07/14/2012",
        "bank_routing_number":"123456789",
        "bank_account_number":"987654321",
        "check_number":"1234",
        "payee":"John Doe",
        "amount":"100.00",
        "authorization_code":"3425678543",
        "status":"A"
      }
    },
    "request_received":"2012-07-14T23:24:37-04:00",
    "response_sent":"2012-07-14T23:24:38-04:00",
    "version":"1"
  }
}

```

Authorizes the use of a convenience check on a customer account. 

### HTTPS Request
`POST /api/convenience_checks(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
identifier | Y | Valid 12-digit customer number |	See Customer Number in Card Account Identifiers.
check_number | Y	| Valid check number previously issued to this customer |	The check serial number on the top right corner of the check
amount | Y |	Valid dollar amount	| The dollar amount of the check.
payee | Y |	Can’t be blank | The name of the person or business receiving the check.


### Response Codes
Code | Description
---- | -----------
801 |	Convenience check could not be authorized.
802	| Convenience check could not be located.
804	| Convenience check has already been authorized.


## Update Convenience Check 
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/convenience_checks/3425678543.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "status":"R"
}
```

> Response

```json
{
  "response":{
    "request_guid":"ae0ee6d8-529e-4589-8d73-b0566ca7359a",
    "success":true,
    "messages":[],
    "data": {
      "convenience_check": {
        "authorized_date":"07/14/2012",
        "bank_routing_number":"123456789",
        "bank_account_number":"987654321",
        "check_number":"1234",
        "payee":"John Doe",
        "amount":"100.00",
        "authorization_code":"3425678543",
        "status":"A"
      }
    },
    "request_received":"2012-07-14T23:24:37-04:00",
    "response_sent":"2012-07-14T23:24:38-04:00",
    "version":"1"
  }
}
```

Updates the status of a previously authorized convenience check. 

### HTTPS Request

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
status | Y | Valid check status| See Check Statuses

### Response Codes
Code | Description
---- | -----------
802 |	Convenience check could not be located.
803	| Convenience check was located in status {0} but could not be updated.

## Verify Convenience Check 
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/convenience_check_verification.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "identifier":"113612345678",
  "check_number":"1234",
  "amount":"100.00"
}
```

> Response

```json
{
  "response":{
    "request_guid":"ae0ee6d8-529e-4589-8d73-b0566ca7359a",
    "success":true,
    "messages":[],
    "data": {
      "convenience_checks":[
        "convenience_check": {
          "authorized_date":"07/14/2012",
          "bank_routing_number":"123456789",
          "bank_account_number":"987654321",
          "check_number":"1234",
          "payee":"John Doe",
          "amount":"100.00",
          "authorization_code":"3425678543",
          "status":"A"
        }
      ],
    },
    "request_received":"2012-07-14T23:24:37-04:00",
    "response_sent":"2012-07-14T23:24:38-04:00",
    "version":"1"
  }
}
```

Finds convenience checks based on known criteria. 

### HTTPS Request
`POST /api/convenience_check_verification(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
authorization_code	| N |	If specified, no other values are required	| Authorization code received when the check was first authorized.
bank_routing_number	| ? |	If specified, requires bank_account_number, check_number and amount | The routing / transit number on the bottom of the check.
bank_account_number	| ?	| If specified, requires bank_routing_number, check_number and amount | The checking account number on the bottom of the check.
identifier	        | N	| Valid 12-digit customer number. If specified, no other values are required.	| See Customer Number in Card Account Identifiers.
check_number	      | ?	| Required if bank_routing_number or bank_account_number is specified. | The check serial number on the top right corner of the check.
amount	            | ?	| Required if bank_routing_number or bank_account_number is specified. | Currency value in the format 100.00	The dollar amount of the check.
start_date	        | N |	Date format: MM/DD/YYYY	| Checks will only be returned if they were created after this date.
end_date	          | N	| Date format: MM/DD/YYYY	| Checks will only be returned if they were created before this date.


### Response Codes
### Response Codes
Code | Description
---- | -----------
819	| Not enough search criteria was provided to locate convenience checks.
820	| There are no records that match your search criteria. 


# Identities
## Create Identity
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/people/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/identity.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"20da9a9e-a1c1-4c6c-90d2-eecf08709796",
    "belongs_to_request_store":"true",
    "success":true,
    "messages":[],
    "data":{
      "person":{
        "use_secondary_mobile_number_for_sms":false,
        "city":"Atlanta",
        "address1":"222333 PEACHTREE PLACE",
        "fax_number":null,
        "id2_issued_on":"",
        "created_at":"07/26/2012",
        "address2":null,
        "payroll_phone_number":null,
        "id1_issued_location":"GA",
        "guid":"9095134d-2879-4b1b-a7c8-274f5a773fa0",
        "updated_at":"07/30/2012",
        "pay_week_of_month":null,
        "postal_code":"30318",
        "id1_issued_on":"",
        "pay_day_of_week":null,
        "pay_date_context_date":null,
        "initial_next_pay_date":"",
        "id2_type":null,
        "security_question_1":"What is your mother's maiden name?",
        "is_employee":false,
        "employer_name_or_source_of_income":null,
        "work_number":null,
        "id2_issued_location":null,
        "id1_type":"1",
        "mobile_number":null,
        "phone_number":"2057551666",
        "id2_number":"",
        "pay_frequency":null,
        "secondary_mobile_number":null,
        "last_name":"SMITH",
        "locale":null,
        "security_question_1_answer":"theanswer",
        "direct_deposit":false,
        "net_pay_each_period":null,
        "email_address":null,
        "date_of_birth":"02/01/1975",
        "middle_initial":null,
        "card_accounts":[{
          "card_account":{
            "created_at":"2012-06-27T17:15:08-04:00",
            "customer_number":"074110832115",
            "guid":"5fc8cc06-17ef-4fc6-b5b8-da5a3eba4a9b",
            "routing_number":"071025797",
            "cards":[{
              "card":{
                "last_four_digits":"2525",
                "created_at":"07/30/2012",
                "guid":"9a65f552-ce07-4737-8512-05a76c37a71d",
                "card_account_guid":"5fc8cc06-17ef-4fc6-b5b8-da5a3eba4a9b",
                "status":"N","balance":null
              }
            }],
            "person_guid":"9095134d-2879-4b1b-a7c8-274f5a773fa0"
          }
        }],
        "id2_expires_on":"",
        "id1_number":"****56524",
        "should_receive_sms_messages":false,
        "postal_region":"AL",
        "id1_expires_on":"11/10/2015",
        "first_name":"JOHN"
      },
      "identity":{
        "response":{
          "results":{
            "message":"ID Located",
            "key":"result.match"
          },
          "id_number":"91027265",
          "qualifiers":{
            "qualifier":{
              "message":"State Does Not Match",
              "key":"resultcode.state.does.not.match"
            }
          },
          "questions":{
            "question":[{
              "type":"current.county",
              "answer":["ATLANTA","FULTON","TOKE","None of the above"],
              "prompt":"In which county have you lived?"
            },
            {
              "type":"prior.residence.state.multiyear",
              "answer":["ILLINOIS","MASSACHUSETTS","NEW YORK","None of the above"],
              "prompt":"Between 1979 and 1980, in which State did you live?"
            },
            {
              "type":"previous.address",
              "answer":["113 BIRCH DR","16015 RACETRACK RD","510 ADAMS RD","None of the above"],
              "prompt":"At which of the following addresses have you lived?"
            },
            {
              "type":"city.of.residence",
              "answer":["MINOT","HILLSIDE","ATLANTA","None of the above"],
              "prompt":"In which city is ANY STREET?"
            },
            {
              "type":"alternate.names.phone",
              "answer":["LIN","LEDFORD","LUTZ","None of the above"],
              "prompt":"With which name are you associated?"
            }]
          },
          "summary_result":{
            "message":"PASS",
            "key":"id.success"
          }
        }
      }
    },
    "request_received":"2012-07-30T14:38:19-04:00",
    "response_sent":"2012-07-30T14:38:21-04:00",
    "version":"3"
  }
}

```

Begins an identity verification session by passing person information.

### HTTPS Request
`POST /api/people/:person_identifier/identity(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------

## Update Identity
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/people/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/identity.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "id_number":"91027265",
  "question1_type":"current.county",
  "question1_answer":"ATLANTA",
  "question2_type":"prior.residence.state.multiyear",
  "question2_answer":"ILLINOIS",
  "question3_type":"previous.address",
  "question3_answer":"113 BIRCH DR",
  "question4_type":"city.of.residence",
  "question4_answer":"MINOT",
  "question5_type":"alternate.names.phone",
  "question5_answer":"LIN"
}

```

> Response

```json
{
  "response":{
    "request_guid":"0e52d8d5-8fa3-48b1-b19b-497a22393954",
    "belongs_to_request_store":"true",
    "success":true,
    "messages":[],
    "data":{
      "person":{
        "use_secondary_mobile_number_for_sms":false,
        "city":"Atlanta",
        "address1":"222333 PEACHTREE PLACE",
        "fax_number":null,
        "id2_issued_on":"",
        "created_at":"07/26/2012",
        "address2":null,
        "payroll_phone_number":null,
        "id1_issued_location":"GA",
        "guid":"9095134d-2879-4b1b-a7c8-274f5a773fa0",
        "updated_at":"07/30/2012",
        "pay_week_of_month":null,
        "postal_code":"30318",
        "id1_issued_on":"",
        "pay_day_of_week":null,
        "pay_date_context_date":null,
        "initial_next_pay_date":"",
        "id2_type":null,
        "security_question_1":"What is your mother's maiden name?",
        "is_employee":false,
        "employer_name_or_source_of_income":null,
        "work_number":null,
        "id2_issued_location":null,
        "id1_type":"1",
        "mobile_number":null,
        "phone_number":"2057551666",
        "id2_number":"",
        "pay_frequency":null,
        "secondary_mobile_number":null,
        "last_name":"SMITH",
        "locale":null,
        "security_question_1_answer":"theanswer",
        "direct_deposit":false,
        "net_pay_each_period":null,
        "email_address":null,
        "date_of_birth":"02/01/1975",
        "middle_initial":null,
        "card_accounts":[{
          "card_account":{
            "created_at":"2012-06-27T17:15:08-04:00",
            "customer_number":"074110832115",
            "guid":"5fc8cc06-17ef-4fc6-b5b8-da5a3eba4a9b",
            "routing_number":"071025797",
            "cards":[{
              "card":{
                "last_four_digits":"2525",
                "created_at":"07/30/2012",
                "guid":"9a65f552-ce07-4737-8512-05a76c37a71d",
                "card_account_guid":"5fc8cc06-17ef-4fc6-b5b8-da5a3eba4a9b",
                "status":"N","balance":null
              }
            }],
            "person_guid":"9095134d-2879-4b1b-a7c8-274f5a773fa0"
          }
        }],
        "id2_expires_on":"",
        "id1_number":"****56524",
        "should_receive_sms_messages":false,
        "postal_region":"AL",
        "id1_expires_on":"11/10/2015",
        "first_name":"JOHN"
      },
      "identity":{
        "response":{
          "idliveq_result":{
            "message":"All answers correct",
            "key":"result.questions.0.incorrect
          "},
          "results":{
            "message":"ID Located",
            "key":"result.match"
          },
          "id_number":"91027265",
          "qualifiers":{
            "qualifier":{
              "message":"State Does Not Match",
              "key":"resultcode.state.does.not.match"
            }
          },
          "answers_received":"5",
          "summary_result":{
            "message":"PASS",
            "key":"id.success"
          },
          "iq_summary_result":"pass"
        }
      }
    },
    "request_received":"2012-07-30T14:52:55-04:00",
    "response_sent":"2012-07-30T14:52:56-04:00",
    "version":"3"
  }
}

```

Completes an identity verification session by passing person information and answers to questions.

### HTTPS Request
`PUT /api/people/:person_identifier/identity(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
question1_type	  | Y	|	from Create Identity
question1_answer	| Y	|	from Create Identity
question2_type	  | Y	|	from Create Identity
question2_answer	| Y	|	from Create Identity
question3_type	  | Y	|	from Create Identity
question3_answer	| Y	|	from Create Identity
question4_type	  | Y	|	from Create Identity
quesiton4_answer	| Y	|	from Create Identity
question5_type	  | Y	|	from Create Identity
question5_answer	| Y	|	from Create Identity
id_number	        | Y	|	from Create Identity


### Response Codes
Code | Description
---- | -----------


# Loads
## Create Load
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/loads.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "amount":"10.00"
}
```

> Response

```json
{
  "response":{
    "request_guid":"fed072d8-1d57-4a1e-8892-024c707407ee",
    "success":true,
    "messages":[],
    "data":{
      "old_balance":"191.60",
      "amount":"10.00",
      "fee_amount":"0.00",
      "transaction_identifier":"Ve8mEK6jUKulAMy7xYGu",
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642"
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      },
      "new_balance":"201.60"
    },
    "request_received":"2012-07-15T12:31:50-04:00",
    "response_sent":"2012-07-15T12:31:53-04:00",
    "version":"1"
  }
}
```

Loads funds to the specified card account on behalf of the store and teller.

### HTTPS Request
`POST /api/card_accounts/:card_account_identifier/loads(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
amount	| Y |	Currency in the format 0.00	Amount of funds to load to the card account.
transaction_type	| N |	Valid transaction type	A valid transaction type to classify the load.

### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME> 	
450	| <AMOUNT> could not be loaded to the active card for the card account. 	
451	| <AMOUNT> could not be loaded to the card account because there is not an active card.	
452	| <AMOUNT> could not be loaded to the card account because it would exceed the load limit. 	


## Reverse Load
> Example Request Query
> <br> 
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/loads/kjh02dfc-4567-9874-82ed-b4b60ab6bba3.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"f140f9a9-df28-4fde-ab35-bdb455dd3670",
    "success":true,
    "messages":[],
    "data":{
      "new_balance":"191.60",
      "transaction_identifier":"Ve8mEK6jUKulAMy7xYGu",
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642"
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      },
      "fee_reversal_amount":"0.00",
      "old_balance":"196.60"
    },
    "request_received":"2012-07-15T12:45:00-04:00",
    "response_sent":"2012-07-15T12:45:00-04:00",
    "version":"1"
  }
}
```

Reverses a previous load on a card account.

### HTTPS Request

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME>
209	| A transaction with transaction id <TRANSACTION ID> could not be found
460	| The load with transaction id <TRANSACTION ID> could not be reversed.
461	| <AMOUNT> exceeds the available funds on the Card Account


## Create Unload
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/unloads.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "amount":"10.00",
  "memo":"Account was loaded in error."
}
```

> Response

```json
{
  "response":{
    "request_guid":"bbbac7f8-984d-42e6-a42c-f96b908953cb",
    "success":true,
    "messages":[],
    "data":{
      "old_balance":"186.60",
      "card_account":{
        "created_at":"07/14/2012",
        "customer_number":"756536607908",
        "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
        "routing_number":"06120642",
        "cards":[
          {
            "card":{
              "created_at":"07/14/2012",
              "last_four_digits":"0000",
              "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
              "status":"N",
              "balance":"743.82"
            }
          }
        ]
      },
      "transaction_identifier":"Ve8mEK6jUKulAMy7xYGu",
      "fee_amount":"0.00",
      "new_balance":"191.60"
    },
    "request_received":"2012-07-15T18:59:01-04:00",
    "response_sent":"2012-07-15T18:59:03-04:00",
    "version":"1"
  }
}

```

Unloads funds from the specified card account.  

### HTTPS Request
`POST /api/card_accounts/:card_account_identifier/unloads(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
amount | Y | Currency in the format 0.00	| Amount of funds to unload.
memo | Y | Alphanumeric	| A description of the unload that will appear in the transaction history.

### Response Codes
Code | Description
---- | -----------


# Notifications
##Create Notification
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/notifications.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "notification_type":"dd_info"
}
```

> Response

```json
{
  "response":{
    "request_guid":"fed072d8-1d57-4a1e-8892-024c707407ee",
    "success":true,
    "messages":[],
    "data":{},
    "request_received":"2012-07-15T12:31:50-04:00",
    "response_sent":"2012-07-15T12:31:53-04:00",
    "version":"3"
  }
}

```

Create a notification to be sent to the cardholder.

### HTTPS Request
`POST /api/card_accounts/:card_account_identifier/notifications(.format)`
### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
notification_type	| Y |	Currently only supports the following type: dd_info	| Specifies the type of notification that should be sent to the cardholder.

### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME> 	
850	| Notification could not be created.	
851	| Invalid notification type provided.	

# People
## Get Person
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/people/ddd05dfc-1234-9874-82ed-b4b60ab6bab8.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response":{
    "request_guid":"ca9a760c-bb0b-4f80-90d9-09ca8c0f52a2",
    "success":true,
    "messages":[],
    "data":{
      "person":{
        "use_secondary_mobile_number_for_sms":false,
        "city":"Birmingham",
        "address1":"600 Beacon Parkway",
        "fax_number":null,
        "id2_issued_on":"01/03/2001",
        "created_at":"07/14/2012",
        "address2":null,
        "payroll_phone_number":"123-123-4567",
        "id1_issued_location":null,
        "guid":"ddd05dfc-1234-9874-82ed-b4b60ab6bab8",
        "updated_at":"07/14/2012",
        "pay_week_of_month":null,
        "postal_code":"35242",
        "id1_issued_on":"",
        "pay_day_of_week":null,
        "pay_date_context_date":null,
        "initial_next_pay_date":"11/12/2012",
        "id2_type":"4",
        "security_question_1":"What is the answer?",
        "is_employee":false,
        "employer_name_or_source_of_income":"Insight",
        "work_number":"",
        "id2_issued_location":"US",
        "id1_type":"0",
        "mobile_number":"1231234567",
        "phone_number":"",
        "id2_number":"***41234",
        "pay_frequency":null,
        "secondary_mobile_number":null,
        "last_name":"Test",
        "card_accounts":[
          {
            "card_account":{
              "created_at":"07/14/2012",
              "customer_number":"756536607908",
              "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
              "routing_number":"06120642",
              "cards":[
                {
                  "card":{
                    "created_at":"07/14/2012",
                    "last_four_digits":"0000",
                    "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
                    "status":"N",
                    "balance":"743.82"
                  }
                }
              ]
            }
          }
        ],
        "locale":"en",
        "security_question_1_answer":"test",
        "direct_deposit":false,
        "net_pay_each_period":null,
        "email_address":null,
        "date_of_birth":"01/02/1990",
        "middle_initial":null,
        "id2_expires_on":"01/03/2015",
        "id1_number":"******-4568",
        "should_receive_sms_messages":false,
        "postal_region":"AL",
        "id1_expires_on":"",
        "first_name":"User"
      }
    },
    "request_received":"2012-07-15T17:15:38-04:00",
    "response_sent":"2012-07-15T17:15:38-04:00",
    "version":"1"
  }
}

```

Gets the attributes of a person.

### HTTPS Request
`GET /api/people/:person_identifier(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
214	| The person with person_identifier <PERSON_IDENTIFIER> could not be found. 

## Update Person
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/card_accounts/kjh02dfc-4567-9874-82ed-b4b60ab6bab8/person.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "person":{
    "city":"Atlanta",
    "address1":"600 Beacon Parkway",
    "postal_code":"35242",
    "mobile_number":"1231234567",
    "should_receive_sms_messages":true,
    "postal_region":"AL"
  }
}
```

> Response

```json
{
  "response":{
    "request_guid":"dc3baa7e-51a1-4bfb-8901-323273fb4011",
    "success":true,
    "messages":[],
    "data":{
      "person":{
        "use_secondary_mobile_number_for_sms":false,
        "city":"Atlanta",
        "address1":"600 Beacon Parkway",
        "fax_number":null,
        "id2_issued_on":"01/03/2001",
        "created_at":"07/14/2012",
        "address2":null,
        "payroll_phone_number":"123-123-4567",
        "id1_issued_location":null,
        "guid":"bf76225c-bd19-4a73-a691-38f513579dc0",
        "updated_at":"07/15/2012",
        "pay_week_of_month":null,
        "postal_code":"35242",
        "id1_issued_on":"",
        "pay_day_of_week":null,
        "pay_date_context_date":null,
        "initial_next_pay_date":"11/12/2012",
        "id2_type":"4",
        "security_question_1":"What is the answer?",
        "is_employee":false,
        "employer_name_or_source_of_income":"Insight",
        "work_number":"",
        "id2_issued_location":"US",
        "id1_type":"0",
        "mobile_number":"1231234567",
        "phone_number":"",
        "id2_number":"***41234",
        "pay_frequency":null,
        "secondary_mobile_number":null,
        "last_name":"Test",
        "locale":"en",
        "security_question_1_answer":"test",
        "direct_deposit":false,
        "net_pay_each_period":null,
        "email_address":null,
        "date_of_birth":"01/02/1990",
        "middle_initial":null,
        "id2_expires_on":"01/03/2015",
        "id1_number":"******-4568",
        "card_accounts":[
          {
            "card_account":{
              "created_at":"07/14/2012",
              "customer_number":"756536607908",
              "guid":"ebd31f60-29ac-452d-a6e7-8771369a0b8e",
              "routing_number":"06120642",
              "cards":[
                {
                  "card":{
                    "created_at":"07/14/2012",
                    "last_four_digits":"0000",
                    "guid":"2bae3443-21dc-42f6-b83f-d140346f361e",
                    "status":"N",
                    "balance":"743.82"
                  }
                }
              ]
            }
          }
        ],
        "should_receive_sms_messages":false,
        "postal_region":"AL",
        "id1_expires_on":"",
        "first_name":"User"
      }
    },
    "request_received":"2012-07-15T19:07:04-04:00",
    "response_sent":"2012-07-15T19:07:06-04:00",
    "version":"1"
  }
}

```

Updates the person associated with the card account. The request should only include the values that need to be updated. 

### HTTPS Request
`PUT /api/card_accounts/:card_account_identifier/person(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
person[first_name]	    | N	| Maximum 20 characters See Note 1	| First Name
person[middle_initial]	| N	| 1 character See Note 1 |	Middle Initial
person[last_name]	      | N	| Maximum 20 characters See Note 1 |	Last Name
person[date_of_birth]	  | N	| Date in the format MM/DD/YYYY	|Date of Birth
person[email_address]	  | N	| Valid email address	| Cardholder’s email address
person[address1]	      | N	| Maximum of 26 characters | Physical address line 1
person[address2]	      | N	| Maximum of 26 characters | Physical address line 2
person[city]	          | N	| Maximum 255 characters | City for physical address
person[postal_region]	  | N	| Valid State	| State that the cardholder resides
person[postal_code]	    | N	| Zip code in the format 00000-0000	| Zip code 
person[should_receive_sms_messages false]	| N |	true or false | Set to true if the cardholder should receive sms messages. Defaults to false. 
person[locale]	              | N |	See Locale table | Language that the user speaks, default is en for English
person[pay_week_of_month]	    | N |	Required if pay frequency is monthly. Valid values are 1,2,3, or 4. |	Week of the month the person is paid
person[pay_day_of_week]	      | N |	Required if pay frequency is monthly. Valid day of week integer, see Days of Week table. | Day of the week that the person is paid
person[initial_next_pay_date]	| N |	MM/DD/YYYY	| Date the cardholder will be paid next
person[pay_frequency]	        | N |	Valid Pay Frequency	| How often cardholder gets paid, see Pay Frequencies table
person[net_pay_each_period]	  | N |	Currency in the format 0.00	| The amount the person is typically paid on each pay period.
person[security_question_1]	  | N |	Maximum 255 characters	| Question for the cardholder to be asked for security when account maintenance is performed
person[security_question_1_answer] | ? | Maximum 255 characters	| The answer to the security question
person[is_employee]	          | N |	true or false	| Set to true if the cardholder is an employee of the Agent
person[employer_name_or_source_of_income]	| N |	Maximum 255 characters | Primary source of income or employer name for cardholder
person[work_number]	                      | N |	000-000-0000 | Work Number
person[phone_number]	                    | N |	"000-000-0000 Required if mobile number blank | Phone Number
person[mobile_number]	                    | N |	"000-000-0000 Required if phone_number blank | Cardholder’s mobile number.

### Response Codes
Code | Description
---- | -----------
206	| A Card Account with card_account_identifier <CARD ACCOUNT IDENTIFIER> could not be found for the Agent <AGENT NAME> 	
490	| The person could not be updated.	

## Search People
> Example Request Query
> <br>
>

> Request

```json
Sample
```

> Response
```json
Sample
```

### HTTPS Request

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
### Response Codes
Code | Description
---- | -----------


# Stores
## Get Stores
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/stores.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response
```json
{
  "response": {
    "request_guid": "1b070a0b-d881-4cb6-a0eb-fdb490398855",
    "success": true,
    "messages": [],
    "data": { 
      "stores":[ 
        {
          "store": {
            "name": "Test Store",
            "guid": "be8f839d-f4e0-4a1c-946c-a972299356cd"
          }
        },
        {
          "store": {
            "name": "Insight Store",
            "guid": "d19c6310-c69f-44ed-9d28-164bc47707d9"
          }
        }
      ]
    },
    "request_received": "2012-07-14T16:24:25-04:00",
    "response_sent": "2012-07-14T16:24:25-04:00",
    "version": "1"
  }
}

```

Gets a list of all stores that belong to the agent. The agent is determined by the login specified in the request.

### HTTPS Request
`GET /api/stores(.format)`

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
404	| Stores enabled belonging to Agent <AGENT> could not be found.

## Get Store
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/stores/ d19c6310-c69f-44ed-9d28-164bc47707d9.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response
```json
{
  "response": {
    "request_guid": "e0250f54-aca3-4abb-ac42-23bf0a07a6f4",   
    "success": true,   
    "messages": [],   
    "data": {
      "store": {
        "billing_address_zip": "35209",
        "billing_address_line_2": "Suite 900",
        "opens_at": "7:30",      
        "address_city": "Homewood",      
        "name": "Insight Store",      
        "closes_at": "21:30",      
        "address_zip": "35209",      
        "guid": "d19c6310-c69f-44ed-9d28-164bc47707d9",      
        "billing_address_state": "AL",      
        "billing_address_city": "Homewood",      
        "phone_number": "205-555-5555",      
        "address_line_1": "600 Beacon Parkway W",      
        "manager_name": null,      
        "address_line_2": "Suite 900",      
        "address_state": "AL",      
        "store_number": "01",      
        "billing_address_line_1": "601 Beacon Parkway W",      
        "daily_limit": 50000   
      }
    },   
    "request_received": "2012-07-14T17:01:55-04:00",   
    "response_sent": "2012-07-14T17:01:55-04:00",   
    "version": "1"
  }
}

```

Gets various attributes of the specified store. 

### HTTPS Request

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
### Response Codes
Code | Description
---- | -----------
204	| A Store with store_identifier <STORE IDENTIFIER> belonging to the Agent <AGENT NAME> could not be found or is disabled.

# Transactions
## Get Transactions
> Example Request Query
> <br>
>

> Request

```json
Sample
```

> Response
```json
Sample
```

### HTTPS Request

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------
### Response Codes
Code | Description
---- | -----------


#Users
## Get User
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/users/19371ed8-06a7-40d5-800f-b77dacaca1ac.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
}
```

> Response

```json
{
  "response": {
    "request_guid": "1289f4c6-534c-4426-8ac1-bdfbe8bfac2c",   
    "success": true,   
    "messages": [],   
    "data": { 
      "user": {
        "name": "Trevor Jones",
        "guid": "19371ed8-06a7-40d5-800f-b77dacaca1ac",
        "stores": [{"store": { "name": "Insight Store",
                               "guid": "d19c6310-c69f-44ed-9d28-164bc47707d9"}}],      
        "roles": [ "Teller" ],
        "login": "tjones",
        "email": "tjones@insightcards1.com"
      }
    },   
    "request_received": "2012-07-14T15:59:19-04:00",
    "response_sent": "2012-07-14T15:59:20-04:00",
    "version": "1"
  }
}
```

### HTTPS Request
`GET https://phase2.insightcards.com/api/users/:user_identifier(.format)`

Gets various attributes of the user. This method will never return the password for the user. 

### Parameters
Parameter	| Req? | Validations | Description
--------- | ---- | ----------- | -----------

### Response Codes
Code | Description
---- | -----------
201	 | The user with user_identifier <USER_IDENTIFIER> could not be found. 


## Create User
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/users.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`


> Request 

```json
{
  "user": {
    "roles": ["Teller"],
    "stores": ["5f076e88-bd68-4a8f-9ea0-3fc502d3f53d"],
    "login": "test-teller3",
    "password": "P@ssword",
    "email": "testuser124@insightcards.com",
    "name": "Tommy Teller",
    "teller_id": "1234"
  }
}
```
> Response

```json
{
  "response": {
    "request_guid": "fcf94dec-d9b2-46ab-b26a-48af70f44d3e",
    "success": true,
    "messages": [],
    "data": {
      "user": {
        "name": "Tommy Teller",
        "guid": "1ecdb044-a053-4713-b8bf-d4db2f7197cd",
        "stores": [{
          "store": {
            "name": "Store #1",
            "guid": "5f076e88-bd68-4a8f-9ea0-3fc502d3f53d"
          }
        }],
        "roles": ["Teller"],
        "login": "test-teller3",
        "teller_id": "1234",
        "email": "testuser124@insightcards.com",
      }
    },
    "request_received": "2012-07-15T13:15:42-04:00",
    "response_sent": "2012-07-15T13:15:42-04:00",
    "version": "1"
  }
}
```  

Creates a new user in the Insight system associated with the specified roles and stores. See the Roles table for valid roles. The Get Stores API method can be used to get a list of valid stores for your agent.

### HTTPS Request

`POST https://phase2.insightcards.com/api/users(.format)`

### Parameters
Name	| Req?	| Validations	| Description
------| ---- | ------------ | -----------
[user[roles][]](#roles)	  | Y	| Array of one or more role names from the Roles table. At least one role is required	| [List of roles that the user should belong to](#roles)
[user[stores][]](#store-identifiers)	| Y |	Array of store GUIDs representing valid stores	List of [store GUIDs](#store-identifiers) to which the teller should be assigned. All stores specified must belong to the agent. A list of valid stores can be obtained using the [Get Stores method](#get-stores).
user[login]	    | Y | Minimum 3 characters, Alphanumeric and .-_@ allowed, Must be unique" | Unique login for the user.
user[password]	| Y	| Minimum 6 characters	|Case sensitive password for the user. Expires after 60 days.
user[name]	    | Y	| Alphanumeric	Full first and last name of the user.
user[email]	    | Y	| Matches email format: you@yourdomain.com., Must be unique |	Unique email address for the user.
user[teller_id]	| ?	| Alphanumeric, Must be unique within the Agent |	Client specified identifier for this teller. Required if roles array contains Teller. 

### Response Codes
Code | Description
---- | -----------
204 |	A Store with store_identifier <STORE IDENTIFIER> belonging to the Agent <AGENT NAME> could not be found or is disabled.
302	| <ROLE> is not valid for the user 
601	| User <LOGIN> could not be created
  
  

## Update User
> Example Request Query
> <br>
> `https://phase2.insightcards.com/api/users/19371ed8-06a7-40d5-800f-b77dacaca1ac.json?login=login&password=password&partner_code=partner&version=version&request_user=user&request_store=store&request_guid=guid`

> Request

```json
{
  "user": {
    "stores": ["19371ed8-06a7-40d5-800f-b77dacaca1ac"],
    "password": "P@ssword",
  }
}
```

> Response

```json
{
  "response": { 
    "request_guid": "b992fb32-b485-4672-ad50-4d1715107830",
    "success": true,   
    "messages": [],   
    "data": {
      "user": {
        "name": "Trevor Jones",      
        "guid": "19371ed8-06a7-40d5-800f-b77dacaca1ac",      
        "stores": [{"store": {
                      "name": "Insight Store",
                      "guid": "d19c6310-c69f-44ed-9d28-164bc47707d9"}}],
        "roles": [ "Teller" ],
        "login": "tjones",      
        "email": "tjones@insightcards1.com"
      }
    },   
    "request_received": "2012-07-14T16:22:41-04:00",   
    "response_sent": "2012-07-14T16:22:41-04:00",   
    "version": "1"
  }
}
```

Updates various attributes of the user. If a parameter is not specified then the value will not be updated. If Roles or Stores need to be updated, the entire list of roles and stores to be assigned to the user should be specified. 

###HTTPS Request

`PUT https://phase2.insightcards.com/api/users/:user_identifier(.format)`

### Parameters

Parameter | Req? | Validations	| Description
------| ---- | ------------ | -----------
user[roles][]	   | Y	| Array of one or more role names from the Roles table. At least one role is required	| List of roles that the user should belong to.
user[stores][]   | Y	| Array of store GUIDs representing valid stores	List of store GUIDs to which the teller should be assigned. All stores specified must belong to the agent. | A list of valid stores can be obtained using the Get Stores method.
user[login]	     | Y	| Minimum 3 characters, Alphanumeric and .-_@ allowed, Must be unique | Unique login for the user.
user[password]	 | Y	| Minimum 6 characters	| Case sensitive password for the user. Expires after 60 days.
user[name]	     | Y  |	Alphanumeric	| Full first and last name of the user.
user[email]	     | Y	| Matches email format: you@yourdomain.com. Must be unique | Unique email address for the user.
user[teller_id]	 | ?	| Alphanumeric, Must be unique within the Agent |	Client specified identifier for this teller. Required if roles array contains Teller. 

<aside class="notice">
Roles must be the entire list of roles and will replace the current list. For example, if the user currently has a Teller role, and you want to add the Store Manager role, then the array should be [“Teller”,”Store Manager”]
</aside>
<aside class="notice">
Stores must be the entire list of stores and will replace the current list. For example, if the user currently has stores 1,2, and 3, and you want to add store 4, then the array should be [“1”,”2”,”3”,”4”]. 
</aside>

### Response Codes
Code | Description 
---- | -----------
204 |	A Store with store_identifier <STORE IDENTIFIER> belonging to the Agent <AGENT NAME> could not be found or is disabled.
302	| <ROLE> is not valid for the user

# Bulk Card Orders
## Get Bulk Card Orders
## Get Bulk Card Order
## Create Bulk Card Order

# Deferred Transactions

# Districts

# Document Templates

# Documents

# Pending Deposits

# Savings Accounts

# Regions

# Products


# Identifiers
Throughout the API, data elements can be identified with a variety of unique keys. Although a GUID is the preferred way of retrieving a particular resource, the API will automatically handle all the alternative identifiers listed below. 

## Card Identifiers
For security reasons, you must have the card GUID in order to retrieve a card. If you do not know the GUID, you can perform a card GUID Search using the PAN. The card GUID should be used in the API where card_identifier is referenced. The card GUID can be stored in any external system without the use of encryption. The PAN should never be stored within your system. 

Name | Format | Description
---- | ------ | -----------
Card GUID	| XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX	| Globally unique identifier for the card, alphanumeric with dashes
PAN	| 0000000000000000 | 16-digit card number, should not be stored.

## Card Account Identifiers
For security reasons, you must have the card account GUID in order to retrieve a card account. If you do not know the GUID, you can perform a card account GUID search using either the Customer Number or the PAN. The card account GUID should be used in the API where card_account_identifier is referenced. The card account GUID can be stored in any external system without the use of encryption. The PAN and/or Customer Number should never be stored within your system.

Name | Format | Description
---- | ------ | -----------
Customer Number	  | 000000000000 | Also known as the PRN or pseudo-dda to be used for ACH. Should always be encrypted when stored.
Card Account GUID | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Globally unique identifier for the card account, alphanumeric with dashes
PAN	| 0000000000000000 | 16 digit card number, should not be stored.

## Store Identifiers
A store can be found using the following identifiers. These identifiers can be used anywhere in the API where store_identifier is referenced. The preferred identifier is the store GUID. 

Name | Format | Description
---- | ------ | -----------
Store GUID | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Globally unique identifier for the store, alphanumeric with dashes
Store Number | Alphanumeric	| Client defined number for the store

## User Identifiers
A user can be found using the following identifiers. These identifiers can be used anywhere in the API where user_identifier is referenced. The preferred identifier is the user GUID. 

Name | Format | Description
---- | ------ | -----------
User GUID	    | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Globally unique identifier for the user, alphanumeric with dashes
Teller Number |	Alphanumeric | Client defined number for the teller. 
User Login	  | Alphanumeric | Login for the user


## Person Identifiers
A person can be found using the following identifiers. These identifiers can be used anywhere in the API where person_identifier is referenced. The preferred identifier is the person GUID. 

Name | Format | Description
---- | ------ | -----------
Person GUID	| XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | Globally unique identifier for the person, alphanumeric with dashes


# Value Descriptions
##Roles
Type | Description
---- | ------------
0	| Teller
1	| Store Manager


##Card Statuses
Status | Description
------ | -----------
N	| Active
L	| Lost
S	| Stolen
C	| Canceled
W	| Waiting for Payment
X	| Set to Emboss
Y	| Shipped/Ready to Activate
Z	| Canceled without refund
D	| Disabled
B	| Blocked
R	| Charged Off
A	| Lost waiting for sufficient funds


##Check Statuses
Status | Description
------ | -----------
U	| Unused
A	| Authorized
R	| Reversed
S	| Stopped
C	| Cleared

##Locales
Name | Description
------ | -----------
en	| English
es	| Spanish

##Fee Plans
Name | Description
------ | -----------
0	| Monthly
1	| Pay as you go

##Identifications
###Primary Identification
Type | Description | Required
---- | ----------- | --------	
0	| Social Security Number | id1_type, id1_number, a valid id2
6	| Matricula Consular Number |	id1_type, id1_number, id1_issued_on, id1_expires_on
9	| Individual Tax Identification Number	| id1_type, id1_number, a valid id2
10	| Tax Identification Number	| id1_type, id1_number, a valid id2
	
###Secondary Identification	
Type | Description | Required
---- | ----------- | --------	
1 |	US State Issued Driver's License | id2_type, id2_number, id2_issued_location (two-letter state abbreviation), id2_issued_on, id2_expires_on
2	| US State Issued ID | id2_type, id2_number, id2_issued_location (two-letter state abbreviation), id2_issued_on, id2_expires_on
3	| US Government Military ID | id2_type, id2_number, id2_issued_on, id2_expires_on
4	| Passport Number	| id2_type, id2_number, id2_issued_location (country), id2_issued_on, id2_expires_on
6	| Matricula Consular Number |	id2_type, id2_number, id2_issued_on, id2_expires_on
8	| US Permanent Residence Card |	id2_type, id2_number, id2_issued_on, id2_expires_on


##CIP Statuses
###Applicant can be accepted; secondary verification not needed
Status | Code | Description
------ | ---- | -----------
Pass |	Pass 01	SSN Matches Name and Address
Pass |	Pass 02	SSN Matches Name and Address
Pass |	Pass 03	Name, Address and DOB Matched, SSN Not Available
Pass |	Pass 04	SSN Matches Name and Address
Pass |	Pass 05	Name, Address and DOB Matched, SSN Not Available
Pass |	Pass 06	SSN Matches Name and Address
Pass |	Pass 07	SSN Matches Name and DOB

###Secondary verification needed (documentary review or out-of-wallet questions)
Status | Code | Description
------ | ---- | -----------
Refer	 | Refer 01	| Nothing Found for input criteria (must apply enhanced documentary review)
Refer	 | Refer 02	| Name and Address Match only, SSN Not Available, No DOB Match
Refer	 | Refer 03	| Name and Address Match only, SSN Not Available, No DOB Match
Refer	 | Refer 04	| SSN Matches Name only, No DOB Match
Refer	 | Refer 05	| Nothing Found for input criteria (must apply enhanced documentary review)

###Applicant must be declined; no secondary verification
Status | Code | Description
------ | ---- | -----------
Fail	 | Fail 01 | SSN Does Not Match Name or Address
Fail	 | Fail 02 | Information associated with different person(s) or high risk indicators
Fail	 | Fail 03 | SSN Does Not Match Name
Fail	 | Fail 04 | SSN Does Not Match Name or Address


# Testing

Authorized API methods may be tested using the Insight test environment with test accounts conditioned to meet the desired scenarios. Insight will provide the appropriate credentials to be used in order to access the API methods, test accounts and partner portal. 

##Test Server URL
`https://phase2.insightcards.com/`

## Testing CIP and Identity Verification

### Test Names

In order to receive consistent and expected CIP results while testing you must use particular values for the cardholder first name, middle initial and last name.

Name | Description
---- | -----------
John P Smith | Results in a CIP Pass response.
John F Smith | Results in a CIP Fail response.
John R Smith | Results in a CIP Refer response.

### Identity Verification Test Data
In order to receive consistent and expected Identity Verification results while testing you must use particular values in the cardholder address, SSN and date of birth. 

Test Values	| Result
----------- | -----------
555 PEACHTREE PLACE <br>Atlanta, GA 30318<br>An SSN ending with 3333<br>A date of birth in February, 1975	| Results in questions being returned.
222333 PEACHTREE PLACE<br>Atlanta, GA 30318<br>An SSN ending with 3333<br>A date of birth in February, 1975	| Results in an automatic Identity Verification Pass.

###Identity Verification Questions and Answers
In order to receive consistent and expected Identity Verification results while testing, please refer to the following questions and their corresponding correct answers. You must answer 3 of 5 questions correctly in order to pass Identity Verification. 

Question | Answer
-------- | ------
What year is your Ford Expedition?	| 2005
In which county have you lived?	| FULTON
Which person is not a relative or someone that you know?	| Person with the last name SMITH
With which name are you associated?	| None of the above
At which of the following addresses have you lived?	| None of the above
How long have you been associated with the property at PEACHTREE PLACE?	| 4 – 5 years
In which city is ANY STREET?	| Atlanta
Between 1979 and 1980, in which State did you live?	| NEW YORK
When did you purchase the property at PEACHTREE PLACE?	| August 1999


# Kittens

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

