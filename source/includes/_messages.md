# Messages

A message will follow a set of rules to be delivered to your customers: 

* API will search for your template, and the configured mechanisms and languages that can be delivered for that specific template version.
* Then will search for your specific customer delivery settings for the group that the template belongs to and the customer preference to be notified with.
* Then will check if the customer has any contact data matching the selected preference mechanism that will be used.
* Then will search for a configured provider that supports that mechanism in your account, and will send one notification for each mechanism with a valid provider. 

Messages can be dinamically filled with your own model structure. And they can also be forced to send in the mechanism you want, to achieve an email verification. 

## Send a message

This endpoint allows you to send messages to you customers with your predefined templates and customer preferences to select the matching selections for delivery the notifications. 

> Request

```curl
curl "https://api.notihub.io/messages/"
  -X POST
  -H "Authorization: Bearer <<your-access-token>>"
  -d '{"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0", "templateId":"email.verification.link", "model": {"displayName": "Sky Walk"}}'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

SendMessageResponse messageResponse = client.messageService().authenticate(accessToken)
        .send(SendMessageRequest.builder()
            .templateId("your-template-id")
            .customerId("your-customer-id")
            .model("{}") // Can be null or any valid json. 
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.messages.send({ 
        templateId: 'your-template-id', 
        customerId: 'your-customer-id',
        force: ['EMAIL'], // optional
        model: {} // optional, value can be any valid json.
    }).then(message => { // Successful response
        console.log(message.messageId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{
  "messageId": "d545490f-46f7-4e31-aaa5-94d06fdf3f60"
}
```

> 404 - Customer Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

> 404 - Template Not Found Response:

```json
{
  "code": 404,
  "message": "Template Not found"
}
```


### HTTP Request

`POST https://api.notihub.io/messages/`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the customer to post a message. 

### Body Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
customerId | String      | Yes | The ID of the target customer
templateId | String      | Yes | The ID of the template to be used in the notification message 
force      | Array       | No | If present, we will force the notification delivery to the mechanism present in this value. It could be an array of the following: `EMAIL`, `PHONE` or `PUSH`
model      | JSON Object | No | A JSON valid data model, that will be used to replace the property values defined in the template

### Response

Parameter | Type | Description
--------- | ------- | -----------
messageId | String | A UUID identifier for your message sent. You can use it to retrieve information about each delivery with their status.






## Retrieve a message

This endpoint allows you to get the delivery information about a message. Note that even we queued a message to be sent inmediately, sent to multiple providers can take some seconds and info will be updated as soon as posible. 

> Request

```curl
curl "https://api.notihub.io/messages/d545490f-46f7-4e31-aaa5-94d06fdf3f60/"
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

GetMessageResponse messageResponse = client.messageService().authenticate(accessToken)
        .get(GetMessageRequest.builder()
        .messageId("your-message-id")
        .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.messages.get('your-message-id')
    .then(message => { // Successful response
        console.log(message.messageId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{
  "messageId": "d545490f-46f7-4e31-aaa5-94d06fdf3f60",
  "request": {
  	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  	"templateId": "users.membership.purchased",
    "version": 1,
    "model": "{'membership': 'GOLD_MEMBER'}"
  },
  "template": {
     "templateId": "users.membership.purchased",
     "templateGroupId": "memberships",
     "language": "es_AR",
     "supportedMechanisms": ["EMAIL", "PUSH"]
  },
  "customer": {
  	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  	"languagePreference": "es_AR",
  	"mechanismPreference": ["PUSH","PHONE"]
  },
  "deliveries": [{
  	"mechanism": "EMAIL",
  	"provider": "SENDGRID",
  	"status": "SKIPPED_BY_CUSTOMER_MECHANISM_PREFERENCE"
  },{
  	"mechanism": "PUSH",
  	"provider": "FIREBASE",
  	"status": "WAITING_IN_QUEUE"
  },{
  	"mechanism": "PHONE",
  	"provider": "TWILIO",
  	"status": "SKIPPED_BY_CUSTOMER_LANGUAGE_PREFERENCE"
  }]
}
```

> 404 - Message Not Found Response:

```json
{
  "code": 404,
  "message": "Message Not found"
}
```


### HTTP Request

`GET https://api.notihub.io/messages/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the message to be retrieved.

### Response

Parameter | Type | Description
--------- | ------- | -----------
messageId | String | A UUID identifier for your message sent. You can use it to retrieve information about deliveries.



## List or search messages

This endpoint allows you to get the delivery information about a message. Note that even we queued a message to be sent inmediately, sent to multiple providers can take some seconds and info will be updated as soon as posible. 

> Request

```curl
curl "https://api.notihub.io/messages/"
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

ListMessageResponse[] messageResponse = client.messageService().authenticate(accessToken)
        .get(ListMessageRequest.builder()
            .limit(10)
            .page(2)
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.messages.list({ limit: 10, page: 1})
    .then(messages => { // Successful response
        console.log(JSON.stringify(messages));
    }).catch(error => {  // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{
  "messageId": "d545490f-46f7-4e31-aaa5-94d06fdf3f60",
  "request": {
  	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  	"templateId": "users.membership.purchased",
    "version": 1,
    "model": "{'membership': 'GOLD_MEMBER'}"
  },
  "template": {
     "templateId": "users.membership.purchased",
     "templateGroupId": "memberships",
     "language": "es_AR",
     "supportedMechanisms": ["EMAIL", "PUSH"]
  },
  "customer": {
  	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  	"languagePreference": "es_AR",
  	"mechanismPreference": ["PUSH","PHONE"]
  },
  "deliveries": [{
  	"mechanism": "EMAIL",
  	"provider": "SENDGRID",
  	"status": "SKIPPED_BY_CUSTOMER_MECHANISM_PREFERENCE"
  },{
  	"mechanism": "PUSH",
  	"provider": "FIREBASE",
  	"status": "WAITING_IN_QUEUE"
  },{
  	"mechanism": "PHONE",
  	"provider": "TWILIO",
  	"status": "SKIPPED_BY_CUSTOMER_LANGUAGE_PREFERENCE"
  }]
}
```

> 404 - Message Not Found Response:

```json
{
  "code": 404,
  "message": "Message Not found"
}
```


### HTTP Request

`GET https://api.notihub.io/messages/`


### Pagination

By default, an embedded list of the 25 last messages are returned. To page through the remaining list, you'll specify a combination of limit and page as query string parameters. Limit can have a max value of 200 customer objects. 

### Example HTTP Request
`GET https://api.notihub.io/messages/?limit=5&page=3`

### Searching Messages
You can search for Messages based on customer by appending the `customerId` or by template appending the `templateId` query string parameter to the endpoint. For example, if you would like to search for Messages sent by some customer you can append the additional parameter search to the request URL.

### Example HTTP Request
`GET https://api.notihub.io/messages/?customerId=0f42b842-007d-4c98-9160-ae6b6e908ca0`


### Query Parameters

Parameter | Type | Required |Description
--------- | ----------- | ----------- | -----------
limit | Number | No | Total max number of elements to return. It can have a max value of 200
page | Number | No | Starting page to return in the query. Start on 0.
customerId | String | No | Filter the results that match the field `customerId` to get messages from a specific Customer
templateId | String | No | Filter the results that match the field `templateId` to get messages using a specific Template


### Response

Parameter | Type | Description
--------- | ------- | -----------
messageId | String | A UUID identifier for your message sent. You can use it to retrieve information about deliveries.
