
# CloudFormation Templates for deploying API Destination resources for private endpoints.


This is the repository contains 2 CloudFormation Templates:

 1. **VPC Lattice Resource Configuration:** 
 This template contains Resource Gateway and configuration. ***Assuming you already have an internal service with a domain name and a security group that allows ingress from the API Destination VPC***. 
 2. **Event Rule with API Destination:** 
 This template can create API destination connection, API destination, event bridge rule with a sample input transformer and the IAM role to allow publishing events to API destination. 

It also has the following sample payload, where Details is actually an embedded JSON string in the `sample-event.json` file. Feel free to edit it as per your need.
 
```
[
  {
    "Source": "test-from-local",
    "Detail": {
      "event-type": "TestSyncUpdate",
      "headers": {
        "message_id": "123",
        "operation_id": "456",
        "transaction_id": "789",
        "event_ts": "2024-04-02T05:17:51.697070",
        "country": "AU"
      },
      "payload": {
        "uuid": "1e05457e-f7d7-4bc7-8b82-76037993025a",
        "created_at": "2020-10-17T09:20:44.493252+11:00",
        "updated_at": "2023-04-14T12:21:37.526507+10:00",
        "type": "business",
        "organisation": {
          "name": "My Org",
          "city": "Melbourne",
          "state": "VIC",
          "postcode": "3000",
          "country": "AU",
          "id": "1234"
        }
      }
    },
    "EventBusName": "default",
    "DetailType": "TestSyncUpdate"
  }
]

```
You can use the following command to test sending events to your event-bridge and trigger the rule once everything is deployed:
`aws events put-events --entries file://sample-event.json --region us-east-1`

The above event payload after going through the input transformer, transforms the request as the following, so that the internal service at the API destination endpoint can then directly consume it:
```
{
	"org": {
		"organisation":{
			"id":"1234",
			"name":"My Org"
		}
	}
}
```

Special credits for actual templates using which I could create this sample: [@emhyy](https://github.com/emhyy)
