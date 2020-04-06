# Customers

This endpoint allows you to register you customers in Notihub platform to send messages using different mechanism like email, sms or even push messages. 

A Customer is identified by us using an auto generated UUID, but it should contain at least one of the following: an `externalId`, a phone number, or an email. 
It can only have one externalId, we recommend to set the id that the customer has in your system, to easily understand who he is, but if you need, you can add any extra info if necessary in the metadata field. 

Keep in mind that even that a customer can have a lot of phone numbers, emails or push tokens, but the messages will only target the primary ones. 

## Create a customer

> Request

```curl
curl "https://api.notihub.io/customers/"
  -X POST
  -H "Authorization: Bearer <<your-access-token>>"
  -d '{"status":"ENABLED", "externalId":"your-customer-id", "language":"en_US", contacts":[{"type":"EMAIL","value":"emailtest@notihub.io","primary":true,"verified":true},{"type":"PHONE","value":"123-444-XXXX","primary":false,"verified":true}]}'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

CreateCustomerResponse customerResponse = client.customerService()
        .authenticate(accessToken)
        .create(CreateCustomerRequest.builder()
            .externalId("my-new-customer7") // An id from you system for easy tracking
            .metadata("some meta") // Metadata not used in Notihub. 
            .status(CustomerStatusType.ENABLED)
            .language("en_US") // can be null if you want to use the default environment language. This allow the customer to override the setting
            .contacts(Arrays.asList(CreateCustomerContactRequest.builder()
                .type(DeliveryMechanismType.EMAIL)
                .value("avalid@email.com")
                .primary(true) // Only one Primary email can exist. It will be used to notify the customer
                .verified(true) // A check when false to avoid sending notifications to templates that require verification 
                .build()))
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customers.create({ 
        externalId: 'your-id', // Id from you system so you can easily access to it.
        status: 'ENABLED', // values: ENABLED or DISABLED
        metadata: 'some-metadata', // optional, metadata to add your custom data, not used in Notihub.
        language: 'en_US', // optional, can be null if you want to use the default environment language. This allow the customer to override the setting.
        contact: [
            {
                type: 'EMAIL',
                value: 'avalid@email.com',
                primary: true,
                verified: true
            }
        ] // optional, array of CustomerContact object
    }).then(customer => { // Successful response
        console.log(customer.customerId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 201 - Successful Response:

```json
{
	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
	"status": "ENABLED",
	"externalId": "your-customer-id",
	"language": "en_US",
	"contacts": [{
		"contactId": 1,
		"type": "EMAIL",
		"value": "emailtest@notihub.io",
		"primary":true,
		"verified":true
	},
	{
		"contactId": 2,
		"type":"PHONE",
		"value":"123-444-XXXX",
		"primary":false, 
		"verified":true
	}]
}
```

> 400 - Bad Request Response:

```json
{
	"code": 400,
	"message": "Customer requires at least a valid email, phone or external id."
}
```

Create a customer with their contact data


### HTTP Request

`POST https://api.notihub.io/customers/`

<aside class="warning">
If you don't set any external id, you will need to set an email or phone at least.
</aside>

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
externalId | String | No | Your own ID to identify this customer in your system. Even that is not required, we recommend to set it for easily tracking with your software.
status | String | No | Current status of the customer. Could be `ENABLED` or `DISABLED`. By default will be in `ENABLED` status
metadata | String | No | Any metadata that you specified for this customer in String format.
language | String      | No | The language that the customer wants to be used to receive notifications
contacts | Array of CustomerContact | No | The contact information of this customer. Contains all the info related to emails, phones, or push tokens to notify him. 


### Customer Contact

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
type      | String | Yes | The type of contact. Should be one of the following: `EMAIL`, `PHONE`, or `PUSH`
value     | String | Yes | The contact value. Can be an email, phone, or a push token. Will be validated against the type chosen.
verified  | String | No  | An indicator that represent for you if this contact data was verified. Useful when you require your email or phone to be verified to sent other templates. Default value is `false`
primary   | String | No  | Tells if is the primary source of contact for the chosen type. We will use it when we need to contact this user with the current mechanism type. Default value is `false`


### Response

Parameter | Type | Description
--------- | ------- | -----------
customerId | String | The UUID autogenerated by Notihub
externalId | String | Your own ID to identify this customer in your system
status | String | Current status of the customer. Could be `ENABLED` or `DISABLED`
language | String      | The ID of the language that the customer wants to be used to receive notifications
metadata | String | Any metadata that you specified for this customer in String format
contacts | Array of CustomerContact | The contact information of this customer. Contains all the info related to emails, phones, or push tokens to notify him. 


## Get a customer

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0"
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

GetCustomerResponse getCustomerResponse = client.customerService().authenticate(accessToken)
       .get(GetCustomerRequest.builder()
           .customerId(customerResponse.getCustomerId())
           .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customers.get('customer-id')
    .then(customer => { // Successful response
        console.log(customer.customerId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{
  "customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  "externalId": "your-custom-id",
  "status": "ENABLED",
  "language": "en_US",
  "metadata": "",
  "contacts": [{
		"contactId": 1,
		"type": "EMAIL",
		"value": "emailtest@notihub.io",
		"primary":true,
		"verified":true
	},
	{
		"contactId": 2,
		"type":"PHONE",
		"value":"123-444-XXXX",
		"primary":false, 
		"verified":true
	}]
}
```

> 404 - Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Returns a customer data, with all their contact info and metadata.


### HTTP Request

`GET https://api.notihub.io/customers/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the customer to retrieve. 

### Response

Parameter | Type | Description
--------- | ------- | -----------
customerId | String | The UUID autogenerated by Notihub
externalId | String | Your own ID to identify this customer in your system
status | String | Current status of the customer. Could be `ENABLED` or `DISABLED`
language | String      | The ID of the language that the customer wants to be used to receive notifications
metadata | String | Any metadata that you specified for this customer in String format
contacts | Array of CustomerContact | The contact information of this customer. Contains all the info related to emails, phones, or push tokens to notify him. 


## Update a customer

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0"
  -X PUT
  -H "Authorization: Bearer <<your-access-token>>"
  -d '{"status":"ENABLED", "externalId":"your-customer-id", "contacts":[{"type":"EMAIL","value":"emailtest@notihub.io","primary":true,"verified":true},{"type":"PHONE","value":"123-444-XXXX","primary":false,"verified":true}]}'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

UpdateCustomerResponse updateCustomerResponse = client.customerService().authenticate(accessToken)
       .update(UpdateCustomerRequest.builder()
            .customerId(cusstomerResponse.getCustomerId())
            .externalId("my-new-customer9")
            .metadata("updated meta")
            .status(CustomerStatusType.ENABLED)
            .language("es_AR")
            .build());

```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customers.update({
        customerId: 'customer-id',
        externalId: 'your-id', // Id from you system so you can easily access to it.
        status: 'ENABLED', // values: ENABLED or DISABLED
        metadata: 'some-metadata', // optional, metadata to add your custom data, not used in Notihub.
        language: 'en_US', // optional, can be null if you want to use the default environment language. This allow the customer to override the setting.
        contact: [
            {
                type: 'EMAIL',
                value: 'avalid@email.com',
                primary: true,
                verified: true
            }
        ] // optional, array of CustomerContact object
    }).then(customer => { // Successful response
        console.log(customer.customerId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 201 - Successful Response:

```json
{
	"customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
	"status": "ENABLED",
	"externalId": "your-new-customer-id",
	"contacts": [{
		"contactId": 1,
		"type": "EMAIL",
		"value": "anotheremail@notihub.io",
		"primary":true,
		"verified":true
	},
	{
		"contactId": 2,
		"type":"PHONE",
		"value":"123-444-XXXX",
		"primary":false, 
		"verified":true
	}]
}
```

> 400 - Bad Request Response:

```json
{
	"code": 400,
	"message": "Customer requires at least a valid email, phone or external id."
}
```

Update a customer without changing their contact data.


### HTTP Request

`PUT https://api.notihub.io/customers/<ID>`

<aside class="warning">
If you don't set any external id, and your customer doesn't has at least an email or phone your request will fail.
</aside>

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
externalId | String | No | Your own ID to identify this customer in your system. Even that is not required, we recommend to set it for easily tracking with your software.
status | String | No | Current status of the customer. Could be `ENABLED` or `DISABLED`. By default will be in `ENABLED` status
metadata | String | No | Any metadata that you specified for this customer in String format.
language | String      | No | The ID of the language that the customer wants to be used to receive notifications


### Response

Parameter | Type | Description
--------- | ------- | -----------
customerId | String | The UUID autogenerated by Notihub
externalId | String | Your own ID to identify this customer in your system
status | String | Current status of the customer. Could be `ENABLED` or `DISABLED`
language | String      | The ID of the language that the customer wants to be used to receive notifications
metadata | String | Any metadata that you specified for this customer in String format
contacts | Array of CustomerContact | The contact information of this customer. Contains all the info related to emails, phones, or push tokens to notify him. 




## Delete a customer

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0"
  -X DELETE
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

client.customerService()
        .authenticate(accessToken)
        .delete(DeleteCustomerRequest.builder()
            .customerId("customer-uuid") 
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customers.delete('customer-id')
    .then(customer => { // Successful response
        console.log(customer.customerId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{}
```

> 404 - Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Delete a customer with his preferences. 

### HTTP Request

`DELETE https://api.notihub.io/customers/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the customer to delete. 



## List and search customers

> Request

```curl
curl "https://api.notihub.io/customers/"
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

ListCustomerResponse[] customerResponse = client.customerService()
         .authenticate(accessToken)
         .list(ListCustomerRequest.builder()
               .page(2)
               .limit(50)    
               .search("some-email@email.com") 
               .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customers.list({ 
        limit: 10, 
        page: 1,
        search: 'some@email.com', // optional
        actives: true // optional
    }).then(customers => { // Successful response
        console.log(JSON.stringify(customers));
    }).catch(error => {  // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
[{
  "customerId": "0f42b842-007d-4c98-9160-ae6b6e908ca0",
  "externalId": "your-custom-id",
  "status": "ENABLED",
  "language": "en_US",
  "metadata": "",
  "contacts": [{
		"contactId": 1,
		"type": "EMAIL",
		"value": "emailtest@notihub.io",
		"primary":true,
		"verified":true
	},
	{
		"contactId": 2,
		"type":"PHONE",
		"value":"123-444-XXXX",
		"primary":false, 
		"verified":true
	}]
},
{
  "customerId": "af32b842-007d-4c98-9160-ae6b6e908ca1",
  "externalId": "your-custom-id-2",
  "status": "DISABLED",
  "language": "es_AR",
  "metadata": "",
  "contacts": [{
		"contactId": 4,
		"type": "EMAIL",
		"value": "emailtest@notihub.io",
		"primary":true,
		"verified":true
	},
	{
		"contactId": 10,
		"type":"PUSH",
		"value":"push-token",
		"primary":false, 
		"verified":true
	}]
}]
```


This section outlines how to retrieve your list of created Customers. Customer search is supported by passing an additional search query string parameter which searches on externalId, email, and phone fields of a Customer.

### HTTP Request

`GET https://api.notihub.io/customers/`

### Pagination

By default, an embedded list of 25 customers are returned and are sorted by created timestamp. To page through the remaining list, you'll specify a combination of `limit` and `page` as query string parameters. Limit can have a max value of 200 customer objects. 

### Example HTTP Request
`GET https://api.notihub.io/customers/?limit=5&page=25`

### Searching Customers
You can search for Customers based on externalId, email or phone by appending the search query string parameter to the endpoint. For example, if you would like to search for Customers with the first name 
"Will", you can append the additional parameter search to the request URL.

### Example HTTP Request
`GET https://api.notihub.io/customers/?search=Jane`


### Query Parameters

Parameter | Type | Required |Description
--------- | ----------- | ----------- | -----------
limit | Number | No | Total max number of elements to return. It can have a max value of 200
page | Number | No | Starting page to return in the query. Start on 0.
search | String | No | Filter the results that match any of the fields `externalId` or `contactData` with the search term
actives | Boolean | No | Filter the results for only enabled users

### Response

Parameter | Type | Description
--------- | ------- | -----------
customerId | String | The UUID autogenerated by Notihub
externalId | String | Your own ID to identify this customer in your system
status | String | Current status of the customer. Could be `ENABLED` or `DISABLED`
language | String      | The language that the customer wants to be used to receive notifications
metadata | String | Any metadata that you specified for this customer in String format
contacts | Array of CustomerContact | The contact information of this customer. Contains all the info related to emails, phones, or push tokens to notify him. 
