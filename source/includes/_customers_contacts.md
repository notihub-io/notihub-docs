# Customer Contacts

This endpoint allows you to create, patch or delete customer contact entries. An entry can be an email, or a phone number or a push token from a device. 

Only the primary contact entries will be used to notify the user, and you can't have more than entry for the same type. 

The verified flag will allow you to send your notifications only if the user verify that entry before. This supports the normal verify email & phone flows. Push tokens are verified by default. 

## Create a contact entry

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0/contacts"
  -X POST
  -H "Authorization: Bearer <<your-access-token>>"
  -d '{"type":"EMAIL","value":"secondtest@notihub.io","primary":false,"verified":true}'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

CreateCustomerContactResponse contactResponse = client.customerContactService()
        .authenticate(accessToken)
        .create(CreateCustomerContactRequest.builder()
            .customerId("customer-id")
            .type(DeliveryMechanismType.EMAIL)
            .value("avalid@email.com")
            .primary(true) // Only one Primary email can exist. It will be used to notify the customer
            .verified(true) // A check when false to avoid sending notifications to templates that require verification 
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customerContacts.create({ 
        customerId: 'customer-id',
        type: 'EMAIL',
        value: 'avalid@email.com',
        primary: true, // Only one Primary email can exist. It will be used to notify the customer
        verified: true // A check when false to avoid sending notifications to templates that require verification
    }).then(customerContact => { // Successful response
        console.log(customerContact.contactId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 201 - Successful Response:

```json
{
	"contactId": 2,
	"type": "EMAIL",
	"value": "secondtest@notihub.io",
	"primary":false,
	"verified":true
}	
```

> 400 - Bad Request Response:

```json
{
	"code": 400,
	"message": "Invalid email"
}
```

> 404 - Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Create a contact data entry for a customer


### HTTP Request

`POST https://api.notihub.io/customers/<ID>/contacts`


### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
type      | String | Yes | The type of contact. Should be one of the following: `EMAIL`, `PHONE`, or `PUSH`
value     | String | Yes | The contact value. Can be an email, phone, or a push token. Will be validated against the type chosen.
verified  | String | No  | An indicator that represent for you if this contact data was verified. Useful when you require your email or phone to be verified to sent other templates. Default value is `false`
primary   | String | No  | Tells if is the primary source of contact for the chosen type. We will use it when we need to contact this user with the current mechanism type. Default value is `false`


### Response

Parameter | Type | Description
--------- | ------- | -----------
contactId | String | The numeric id of this entry made by Notihub
type      | String | Yes | The type of contact. Should be one of the following: `EMAIL`, `PHONE`, or `PUSH`
value     | String | Yes | The contact value. Can be an email, phone, or a push token. Will be validated against the type chosen.
verified  | String | No  | An indicator that represent for you if this contact data was verified. Useful when you require your email or phone to be verified to sent other templates. Default value is `false`
primary   | String | No  | Tells if is the primary source of contact for the chosen type. We will use it when we need to contact this user with the current mechanism type. Default value is `false`


## Update a contact entry

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0/contacts/2"
  -X PUT
  -H "Authorization: Bearer <<your-access-token>>"
  -d '{"type":"EMAIL","value":"second-third@notihub.io","primary":false,"verified":false}'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

UpdateCustomerContactResponse contactResponse = client.customerContactService()
        .authenticate(accessToken)
        .update(UpdateCustomerContactRequest.builder()
            .contactId(3)
            .customerId("customer-id")
            .type(DeliveryMechanismType.EMAIL)
            .value("avalid@email.com")
            .primary(true) // Only one Primary email can exist. It will be used to notify the customer
            .verified(true) // A check when false to avoid sending notifications to templates that require verification 
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customerContacts.update({ 
        contactId: 'contact-id',
        customerId: 'customer-id',
        type: 'EMAIL',
        value: 'avalid@email.com',
        primary: true, // Only one Primary email can exist. It will be used to notify the customer
        verified: true // A check when false to avoid sending notifications to templates that require verification
    }).then(customerContact => { // Successful response
        console.log(customerContact.contactId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{
	"contactId": 2,
	"type": "EMAIL",
	"value": "second-third@notihub.io",
	"primary":false,
	"verified":false
}	
```

> 400 - Bad Request Response:

```json
{
	"code": 400,
	"message": "Invalid email"
}
```

> 404 - Contact Entry Not Found Response:

```json
{
  "code": 404,
  "message": "Contact Entry Not found"
}
```

> 404 - Customer Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Update a contact data entry for a customer


### HTTP Request

`PUT https://api.notihub.io/customers/<Customer-ID>/contacts/<ID>`


### URL Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
Customer-ID| String | Yes | The UUID of the customer
ID         | Number | Yes | The contact entry id 

### Body Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
type      | String | Yes | The type of contact. Should be one of the following: `EMAIL`, `PHONE`, or `PUSH`
value     | String | Yes | The contact value. Can be an email, phone, or a push token. Will be validated against the type chosen.
verified  | String | No  | An indicator that represent for you if this contact data was verified. Useful when you require your email or phone to be verified to sent other templates. Default value is `false`
primary   | String | No  | Tells if is the primary source of contact for the chosen type. We will use it when we need to contact this user with the current mechanism type. Default value is `false`


### Response

Parameter | Type | Description
--------- | ------- | -----------
contactId | String | The numeric id of this entry made by Notihub
type      | String | Yes | The type of contact. Should be one of the following: `EMAIL`, `PHONE`, or `PUSH`
value     | String | Yes | The contact value. Can be an email, phone, or a push token. Will be validated against the type chosen.
verified  | String | No  | An indicator that represent for you if this contact data was verified. Useful when you require your email or phone to be verified to sent other templates. Default value is `false`
primary   | String | No  | Tells if is the primary source of contact for the chosen type. We will use it when we need to contact this user with the current mechanism type. Default value is `false`



## Delete a contact entry

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0/contacts/2"
  -X DELETE
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

client.customerContactService()
        .authenticate(accessToken)
        .delete(DeleteCustomerRequest.builder()
            .contactId(3)
            .customerId("customer-id")
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customerContacts.delete('contact-id', 'customer-id')
    .then(() => { // Successful response
        // Empty response
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
{}	
```

> 404 - Contact Entry Not Found Response:

```json
{
  "code": 404,
  "message": "Contact Entry Not found"
}
```

> 404 - Customer Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Delete a contact data entry for a customer


### HTTP Request

`DELETE https://api.notihub.io/customers/<Customer-ID>/contacts/<ID>`


### URL Parameters

Parameter | Type | Required | Description
--------- | ------- | -----------| -----------
Customer-ID| String | Yes | The UUID of the customer
ID         | Number | Yes | The contact entry id 
