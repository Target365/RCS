# REST User Guide

## Table of Contents
* [Introduction](#introduction)
* [Generate API key](#generate-api-key)
* [Setup](#setup)
    * [Authorization](#authorization)
    * [Swagger](#swagger)
* [Text messages](#text-messages)
    * [Send an SMS](#send-an-sms)
    * [Send a batch of SMS](#send-a-batch-of-sms)
    * [Set DeliveryReport URL for an SMS](#set-deliveryreport-url-for-an-sms)
    * [Send an SMS with payment](#send-an-sms-with-payment)

## Introduction
The Target365 RCS REST API gives you direct access to our online RCS services for sending RCS messages, also supporting Strex and Vipps payment. You can provide webhooks for receiving messages and delivery reports from endusers.

## Generate API key
* Log in to your account at https://www.strexconnect.no
* Select "API keys" from the gear icon in the upper right corner
* Click the button "Add API key"
* Enter a name for the key
* Add an expiry date for the new API key. The system suggests a date 365 days from now. Max expiry is 3 years from now
* Click "Save"
* Copy the "API key" string

## Setup
### Authorization
* Add a header called "X-ApiKey" with the key from above to your requests.

### Swagger
* Swagger for the API is located here: https://shared.target365.io/api

## Text messages

### Send a Text RCS
This example sends a text RCS to 12345678 (+47 for Norway) from the agent "TestBot" with the text "Hello world from RCS!".

#### Request
```
POST https://test.target365.io/api/rcs-messages
Content-Type: application/json

{
  "agent": "TestBot",
  "type": "Text",
  "msisdn": "+4712345678",
  "text": "Hello world from RCS!"
}
```
  
#### Response
```
201 Created
Location: https://test.target365.io/api/rcs-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.

### Send a Card RCS
This example sends a card RCS to 12345678 (+47 for Norway) from the agent "TestBot" with an image, a title and a description.

#### Request
```
POST https://test.target365.io/api/rcs-messages
Content-Type: application/json

{
  "agent": "TestBot",
  "type": "Card",
  "msisdn": "+4712345678",
  "contents": [
    {
	  "fileUrl": "https://target365shared.blob.core.windows.net/rcsimages-3/t365logo.png",
	  "title": "Welcome",
	  "description": "Please select an option",
	  "suggestions": [
		  { "displayText": "Option number 1", "postBackData": "1" },
		  { "displayText": "Option number 2", "postBackData": "2" }
	  ],
	}
  ]
}
```
  
#### Response
```
201 Created
Location: https://test.target365.io/api/rcs-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.
