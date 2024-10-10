# REST User Guide

## Table of Contents
* [Introduction](#introduction)
* [Generate API key](#generate-api-key)
* [Setup](#setup)
    * [Authorization](#authorization)
    * [Swagger](#swagger)
* [RCS messages](#rcs-messages)
    * [Send a Text](#send-a-text)
    * [Send a Card](#send-a-card)
    * [Send a Carousel](#send-a-carousel)
    * [Send payment buttons](#send-payment-buttons)
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

## RCS messages

### Send a Text
This example sends a text RCS to 12345678 (+47 for Norway) from the agent "TestBot" with the text "Hello world from RCS!" and 2 buttons.

#### Request
```
POST https://test.target365.io/api/rcs-messages
Content-Type: application/json

{
  "agent": "TestBot",
  "type": "Text",
  "msisdn": "+4712345678",
  "text": "Hello world from RCS!",
  "suggestions": [
	  { "displayText": "Option number 1", "postBackData": "1" },
	  { "displayText": "Option number 2", "postBackData": "2" }
  ],
}
```
  
### Send a Card
This example sends a card RCS to 12345678 (+47 for Norway) from the agent "TestBot" with an image, a title, description and 2 buttons.

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
  
### Send a Carousel
This example sends a carousel RCS to 12345678 (+47 for Norway) from the agent "TestBot" with 2 cards.

#### Request
```
POST https://test.target365.io/api/rcs-messages
Content-Type: application/json

{
  "agent": "TestBot",
  "type": "Carousel",
  "msisdn": "+4712345678",
  "contents": [
    {
      "fileUrl": "https://target365shared.blob.core.windows.net/rcsimages-3/t365logo.png",
      "title": "Card 1",
      "description": "Please select an option",
      "suggestions": [
        { "displayText": "Option number 1", "postBackData": "1" },
        { "displayText": "Option number 2", "postBackData": "2" }
      ]
    },
    {
      "fileUrl": "https://target365shared.blob.core.windows.net/rcsimages-3/t365logo.png",
      "title": "Card 2",
      "description": "Please select an option",
      "suggestions": [
        { "displayText": "Option number 3", "postBackData": "3" },
        { "displayText": "Option number 4", "postBackData": "4" }
      ]
    }
  ]
}
```
  
### Send a Carousel
This example sends a carousel RCS to 12345678 (+47 for Norway) from the agent "TestBot" with 2 cards.

#### Request
```
POST https://test.target365.io/api/rcs-messages
Content-Type: application/json

{
  "agent": "TestBot",
  "type": "Carousel",
  "msisdn": "+4712345678",
  "contents": [
    {
      "fileUrl": "https://target365shared.blob.core.windows.net/rcsimages-3/t365logo.png",
      "title": "Card 1",
      "description": "Please select an option",
      "suggestions": [
        { "displayText": "Option number 1", "postBackData": "1" },
        { "displayText": "Option number 2", "postBackData": "2" }
      ]
    },
    {
      "fileUrl": "https://target365shared.blob.core.windows.net/rcsimages-3/t365logo.png",
      "title": "Card 2",
      "description": "Please select an option",
      "suggestions": [
        { "displayText": "Option number 3", "postBackData": "3" },
        { "displayText": "Option number 4", "postBackData": "4" }
      ]
    }
  ]
}
```
  
### Send payment buttons
The RCS API is connected to the Checkout service in Strex Connect, and you can send payment buttons connected to a Checkout keyword to integrate payment into your RCS agent.
This example sends a card RCS to 12345678 (+47 for Norway) from the agent "TestBot" with 2 payment buttons connected to a keyword by it's keywordId. You can find the Id for your keyword in the address field of your browser by clicking the keyword in the left menu in Strex Connect.

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
	  "description": "Buy this product.",
	  "suggestions": [
		  { "postBackData": "{\"action\":\"StrexPayment\",\"price\":1.0,\"keywordId\":\"d0d2f211-2bd0-4e4a-9c62-6f73c0490e73\",\"webhookUrl\": \"https://enw6bruw0hfch.x.pipedream.net/strex\"}" },
		  { "postBackData": "{\"action\":\"VippsPayment\",\"price\":1.0,\"keywordId\":\"d0d2f211-2bd0-4e4a-9c62-6f73c0490e73\",\"webhookUrl\": \"https://enw6bruw0hfch.x.pipedream.net/vipps\"}" }
	  ],
	}
  ]
}
```
The payment buttons are provided in the postBackData of the suggestion, and must be provided as a string (escaped quotes). Which provider the button triggers are set to the action parameter. Two providers are supported:
* StrexPayment
* VippsPayment

A status request with the result of the payment will be sent to the provided webhookUrl, so you can act on it and send the user a message back. The data posted looks like this:
```
{
  "transactionId": "294ba7be-4202-4721-a3e1-d7dde9aa7d85",
  "paymentStatus": "Ok",
  "paymentDetailedStatus": "Success",
  "paymentTransactionId": "294ba7be-4202-4721-a3e1-d7dde9aa7d85",
  "correlationId":"MxWP5Vp79XRTqivnYPRY6-Xg"
}
```
* transactionId is the Checkout transaction Id
* paymentStatus can be "Ok" or "Failed"
* paymentDetailedStatus contains a description of the status from the provider
* paymentTransactionId is the Id used against the provider, usually the same as transctionId
* correlationId is the id for the corresponding RCS in-message so the payment can be linked to the relevant message

#### Response
```
201 Created
Location: https://test.target365.io/api/rcs-messages/8eb5e79d-0b3d-4e50-a4dd-7a939af4c4c3

```

#### Response codes
* 201	Out-message posted successfully. Location HTTP-header will contain resource uri.
* 400	Request had invalid payload.
* 401	Request was unauthorized.
