# Example data (replace with your own)

```
API_KEY=0123your-api-key123123412481284798127422
CALL_TO=3712220000
```

## Data API: Retrieve the numeric ID for employee SIP line
You need to obtain ID from `result.data[].employee_id` and SIP number from `result.data[].phone_number`. 

### Example request
```json
{
    "jsonrpc": "2.0",
    "id": "Your-App-Name",
    "method": "get.sip_lines",
    "params": {
        "access_token": "0123your-api-key123123412481284798127422"
}
```
### Example response
In this example, you would obtain `EMPLOYEE_ID=10110` and `EMPLOYEE_SIP=0123456`
```json
{
  "jsonrpc": "2.0",
  "id": "Your-App-Name",
  "result": {
    "data": [
      {
        "id": 12312,
        "type": "in_out",
        "status": "active",
        "password": "aaaaaaaaaa",
        "dial_time": 60,
        "employee_id": 10110,
        "ip_addresses": [
          {
            "ip": "10.81.120.12",
            "date_time": "2025-03-20T11:21:38"
          }
        ],
        "phone_number": "0123456",
        "billing_state": "active",
        "channels_count": 1,
        "physical_state": "Registered",
        "employee_full_name": "Employee Name",
        "virtual_phone_number": "371222333",
        "virtual_phone_usage_rule": "fixed"
      }
    ],
    "metadata": {
      "total_items": 130,
      "version": null,
      "limits": {
        "day_limit": 50000,
        "day_remaining": 49990,
        "day_reset": 46737,
        "minute_limit": 250,
        "minute_remaining": 249,
        "minute_reset": 57
      }
    }
  }
}
```

### Example script
```bash
curl https://dataapi.octella.com/v2.0 -H 'accept: application/json' -H 'content-type: application/json' --data-raw "{
    \"jsonrpc\": \"2.0\",
    \"id\": \"Your-App-Name\",
    \"method\": \"get.sip_lines\",
    \"params\": {
        \"access_token\": \"$API_KEY\"
    }
}" > get.sip_lines.json

EMPLOYEE_ID=$(cat get.sip_lines.json | jq -r ".result.data[] | select (.employee_full_name==\"Employee Name\") | .employee_id")
EMPLOYEE_SIP=$(cat get.sip_lines.json | jq -r ".result.data[] | select (.employee_full_name==\"Employee Name\") | .phone_number")

```

## Data API: Retrieve number group ID to use

### Example request
```json
{
  "jsonrpc": "2.0",
  "id": "Your-App-Name",
  "method": "get.virtual_number_groups",
  "params": {
    "access_token": "0123your-api-key123123412481284798127422"
  }
}
```

### Example response
On app.octella.com the group ID is in format `g123`, in the API you have to use just a number  `123`
```json
{
  "jsonrpc": "2.0",
  "id": "Your-App-Name",
  "result": {
    "data": [
      {
        "id": 2272,
        "name": "C2C Test",
        "number": "g123",
        "employees": [
          {
            "id": 112233,
            "number": "371223344"
          }
        ],
        "employee_groups": [
          {
            "id": 11221,
            "name": "Administrators"
          },
          {
            "id": 11222,
            "name": "Operators"
          },
          {
            "id": 11223,
            "name": "Team Lead"
          }
        ]
      }
    ],
    "metadata": {
      "total_items": 1,
      "version": null,
      "limits": {
        "day_limit": 50000,
        "day_remaining": 49989,
        "day_reset": 46737,
        "minute_limit": 250,
        "minute_remaining": 248,
        "minute_reset": 57
      }
    }
  }
}
```
### Example script
```bash
curl https://dataapi.octella.com/v2.0 -H 'accept: application/json' -H 'content-type: application/json' --data-raw "{
    \"jsonrpc\": \"2.0\",
    \"id\": \"Your-App-Name\",
    \"method\": \"get.virtual_number_groups\",
    \"params\": {
        \"access_token\": \"$API_KEY\"
    }
}" > get.virtual_number_groups.json

NUMBER_GROUP=$(cat get.virtual_number_groups.json | jq -r ".result.data[].number" | sed 's#^g##' | head -n1)


```

## Call API: Starting a call

### Example request:
```json
{
  "jsonrpc": "2.0",
  "id": "Your-App-Name",
  "method": "start.employee_call",
  "params": {
    "access_token": "0123your-api-key123123412481284798127422",
    "contact": "3712220000",
    "direction": "out",
    "employee": {
      "id": 10110,
      "phone_number": "0123456"
    },
    "first_call": "employee",
    "virtual_number_group": 123,
    "virtual_phone_usage_rule": "random"
  }
}
```
### Example response:
```json
{
  "jsonrpc": "2.0",
  "id": "Your-App-Name",
  "result": {
    "data": {
      "call_session_id": 73622361
    },
    "metadata": {
      "limits": {
        "day_limit": 50000,
        "day_remaining": 49999,
        "day_reset": 48139,
        "minute_limit": 250,
        "minute_remaining": 249,
        "minute_reset": 19
      }
    }
  }
}
```
### Example script:
```bash
curl https://callapi.octella.com/v4.0 -H 'accept: application/json' -H 'content-type: application/json' --data-raw "{
    \"jsonrpc\": \"2.0\",
    \"id\": \"Your-App-Name\",
    \"method\": \"start.employee_call\",
    \"params\": {
        \"access_token\": \"$API_KEY\",
        \"contact\": \"$CALL_TO\", 
        \"direction\": \"out\",
        \"employee\": { 
          \"id\": $EMPLOYEE_ID, 
          \"phone_number\": \"$EMPLOYEE_SIP\"
        }, 
        \"first_call\": \"employee\", 
        \"virtual_number_group\": $NUMBER_GROUP, 
        \"virtual_phone_usage_rule\": \"random\"
    }
}" > start.employee_call.json
CALL_SESSION_ID=$(cat start.employee_call.json | jq -r ".result.data.call_session_id" )

```
