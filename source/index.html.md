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

### Response Codes

# Alerts
## Get Alerts
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

### Response Codes

## Set Alerts
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

### Response Codes

# API Requests
## Get API Request
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

### Response Codes

# Available Methods
## Get Available Methods
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

### Response Codes

## Card Accounts
## Get Card Account
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

### Response Codes
## Get Card Account Guid
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

### Response Codes

# Cards
## Create Card
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

### Response Codes

## Update Card
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

### Response Codes

## Get Card Guid

# Convenience Checks
## Create Convenience Check 
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

### Response Codes

## Update Convenience Check 
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

### Response Codes

## Verify Convenience Check 
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

### Response Codes

# Identities
## Create Identity
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

### Response Codes

## Update Identity
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

### Response Codes


# Loads
## Create Load
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

### Response Codes

## Reverse Load
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

### Response Codes

## Create Unload
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

### Response Codes


# Notifications
##Create Notification
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

### Response Codes


# People
## Get Person
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

### Response Codes

## Update Person
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

### Response Codes

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

### Response Codes


# Stores
## Get Stores
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

### Response Codes


## Get Store
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

### Response Codes


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

### Response Codes


#Users
## Get User
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

### Response Codes

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

