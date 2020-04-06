# Customer Preferences

This endpoint allows you to register, update and retrieve you customer preferences. A customer can enable or disable different mechanism types for different groups of notifications. 

If customer doesn't have any preference set at all, a notification message will use the mechanism supported for the specified template. 

## Create or update customer preferences

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0/preferences/"
  -X POST
  -H "Authorization: Bearer <<your-access-token>>"
  -d '
  [{
  	"templateGroupId": "system.products",
  	"mechanisms": ["EMAIL","PUSH"],
  },{
  	"templateGroupId": "system.friends",
  	"mechanisms": ["PUSH"],
  }]
'
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();

CreateCustomerPreferenceResponse createCustomerPreferenceResponse = client.customerService().authenticate(accessToken)
        .get(CreateCustomerPreferenceRequest.builder()
            .customerId("customer-id")
            .templateGroupId("template-group-id")
            .mechanisms(Arrays.asList(DeliveryMechanismType.EMAIL, DeliveryMechanismType.PUSH))
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customerContacts.createOrUpdate({ 
        customerId: 'customer-id',
        templateGroupId: 'template-group-id',
        mechanisms: ['EMAIL', 'PUSH'] // Values can be EMAIL, PUSH and/or SMS. NONE is used to turn off.
    }).then(customerPreference => { // Successful response
        console.log(customerPreference.templateGroupId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 201 - Successful Response:

```json
[{
  	"templateGroupId": "system.products",
  	"mechanisms": ["EMAIL","PUSH"]
  },{
  	"templateGroupId": "system.friends",
  	"mechanisms": ["PUSH"]
  }]
```

> 404 - Customer Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

### HTTP Request

`POST https://api.notihub.io/customers/<ID>/preferences/`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the customer to create a preference. 

### Body Parameters

Parameter | Type | Required | Description
--------- | ----------- | ----------- | -----------
templateGroupId | String      | Yes | The ID of the matching group that template mechanism settings will be overwritten by this customer
mechanisms      | Array       | No | An array of values `EMAIL`, `PHONE`, `PUSH`, or `NONE` to turn off. 


### Response

Parameter | Type | Description
--------- | ------- | -----------
templateGroupId | String      |  The ID of the matching group that template mechanism settings will be overwritten by this customer
mechanisms      | Array       | An array of values `EMAIL`, `PHONE`, `PUSH`, or `NONE` to turn off. 



## Retrieve customer preferences

> Request

```curl
curl "https://api.notihub.io/customers/0f42b842-007d-4c98-9160-ae6b6e908ca0/preferences/"
  -H "Authorization: Bearer <<your-access-token>>"
```

```java
NotihubClient client = buildClient(); // See Authentication
NotihubAccessToken accessToken = client.authenticationService().exchangeCredentials();
        
GetCustomerPreferenceResponse getCustomerPreferenceResponse = client.customerService().authenticate(accessToken)
        .get(GetCustomerPreferenceRequest.builder()
            .customerId("customer-id")
            .build());
```

```js
const notihub = new Notihub({ ...yourKeys });

notihub.customerContacts.get('customer-id')
    .then(customerPreference => { // Successful response
        console.log(customerPreference.templateGroupId);
    }).catch(error => { // Catch error here.
        console.error(error)
    });
```

> 200 - Successful Response:

```json
[{
  	"templateGroupId": "system.products",
  	"mechanisms": ["EMAIL","PUSH"]
  },{
  	"templateGroupId": "system.friends",
  	"mechanisms": ["PUSH"]
 }]

```

> 404 - Customer Not Found Response:

```json
{
  "code": 404,
  "message": "Customer Not found"
}
```

Get the current customer preferences.

### HTTP Request

`GET https://api.notihub.io/customers/<ID>/preferences/`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the customer to retrieve his preferences. 

### Response

Parameter | Type | Description
--------- | ------- | -----------
templateGroupId | String      | The ID of the matching group that template mechanism settings will be overwritten by this customer
mechanisms      | Array       | An array of values `EMAIL`, `PHONE`, `PUSH`, or `NONE` to turn off. 

