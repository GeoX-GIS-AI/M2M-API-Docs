# Overview
This API allows you to search properties from our vast range of footprints data.

# Autorization
The API requires to be signed with Version 4 signing process of AWS. Here is how we can do it in different ways:

## Postman
In the Authorization tab of Postman app, do the following:
- For Type, choose AWS Signature.
- For AccessKey and SecretKey, enter your IAM access key ID and secret access key.

## Python
We can install and use the AWS Requests Auth library from [here](https://pypi.org/project/aws-requests-auth/)

## Manual
To manually sign your requests using another tool or environment,
use the [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html).
For more information, see [Signing requests](https://docs.aws.amazon.com/apigateway/api-reference/signing-requests/).


# HTTP response codes
- 200 — Success.
- 400 — Bad Request There was a validation error with the input. Most probably you missed to provide lat/lng or correlationId in the request body.
- 401 — You Signging process was failed. Probably due to incorrect AWS Access Key/Secret Key.
- 403 — Forbidden.
- 404 — Not Found One or more of the required resources was not found, please make sure you entered correct endpoint URL.
- 500 — Internal Server Error This is an issue with our servers processing your request. In most cases we are notified so that we can investigate the issue.

# Getting Started

## Postman
Postman is an amazing application. It's a tool that allows developers to make HTTP requests extremely easy, save examples, work under different environments, we LOVE it!

Postman is downloadable for free use from here and supported with lots of developer docs here.We are providing you the full collection of this API for your usage with Postman, this collection includes examples of different requests & responses, the endpoints, documentation to immensely ease your interaction with our API.

After downloading Postman follow the instructions below to get started.

=> Single Location API with GET method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v8.4/parcels
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your lat/lng or address in the Params section with `lat` and `lng`  and `address` keys.
6. Finally, hit the API.

=> Batch Location API with POST method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v8.4/parcels
3. Select the POST HTTP method
4. Setup authorization as mentioned above.
5. Now go to the Body section, select the raw radio button and select JSON from the dropdown.
6. Now put your lat/lng or address in the Body section with `lat` and `lng`  and `address` keys along with `correlationId` key.
7. You can add multiple locations in the body section. Please refer to API documentation for more details (link at the end of this document).
8. Finally, hit the API.

## Access API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth

### Python code for both single and batch requests
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def m2m_request_single_location(access_key, secret_key, lat=None, lng=None, address=None):
    """
    This function will hit the API with single location and return the response.
    :param access_key: AWS Access Key
    :param secret_key: AWS Secret Key
    :param lat: Latitude of the location
    :param lng: Longitude of the location
    :param address: Address of the location
    :return: Response of the API
    """
    assert (lat and lng) or address, "Either lat/lng or address is required."
    api_url = "https://api.geox-ai.com/api/v8.4/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    if (lat and lng):
        params = {
            "lat": lat,
            "lng": lng
        }
    else:
        params = {
            "address": address
        }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data

def m2m_locations_batch(access_key, secret_key, locations):
    api_url = "https://api.geox-ai.com/api/v8.4/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    request_body = {
        "locations": locations
    }
    res = requests.post(api_url, auth=auth, json=request_body)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    # Calling single location API with lat/lng
    m2m_response = m2m_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               lat=45.6696163800542, lng=-122.5038830583887)
    print(json.dumps(m2m_response, indent=2))

    # Calling single location API with address
    m2m_response = m2m_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               address="123 Main St, Portland, OR 97204")
    print(json.dumps(m2m_response, indent=2))

    # Calling batch location API
    locations = [ # Each location object should contain lat/lng or address along with correlationId
        {
            "lat": 45.6696163800542,
            "lng": -122.5038830583887,
            "correlationId": "123"
        },
        {
            "address": "123 Main St, Portland, OR 97204",
            "correlationId": "789"
        }
    ]
    m2m_response = m2m_locations_batch(access_key, secret_key, locations)

```

## Access API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) for more details.

=> Single Location API with GET method
```shell
curl --location --request GET 'https://api.geox-ai.com/api/v8.4/parcels?lat=33.970191989230656&lng=-84.34141973584119' \
--header 'X-Amz-Date: 20221114T085525Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXXXXX/20221114/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90bXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

=> Batch Location API with POST method
```shell
curl --location --request POST 'https://api.geox-ai.com/api/v8.4/parcels' \
--header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
--header 'X-Amz-Date: 20221119T095635Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFXXXXXXX/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc33192f8d7c9802b2e0df49XXXXXXXXXXXX' \
--header 'Content-Type: application/json' \
--data-raw '{
    "locations": [
        {
            "lat": 45.6696163800542,
            "lng": -122.5038830583887,
            "correlationId": "123"
        },
        {
            "address": "123 Main St, Portland, OR 97204",
            "correlationId": "789"
        }
    ]
}'
```

## Access API with wget
=> Single Location API with GET method
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20221114T085525Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TIXXX/20221114/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=021611bd2dba2e3f90XXX' \
   'https://api.geox-ai.com/api/v8.4/parcels?lat=33.970191989230656&lng=-84.34141973584119'
```

=> Batch Location API with POST method
```shell
wget --no-check-certificate --quiet \
  --method POST \
  --timeout=0 \
  --header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
  --header 'X-Amz-Date: 20221119T095635Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2XXX/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=74891c783cc331XXX' \
  --header 'Content-Type: application/json' \
  --body-data '{
    "locations": [
        {
            "lat": 45.6696163800542,
            "lng": -122.5038830583887,
            "correlationId": "123"
        },
        {
            "address": "123 Main St, Portland, OR 97204",
            "correlationId": "789"
        }
    ]
}' \
   'https://api.geox-ai.com/api/v8.4/parcels'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.geox-ai.com/api/v8.4/parcels
```

## Request Query params Sample (Single Location API)

=> With lat/lng
```shell
lat=33.970191989230656&lng=-84.34141973584119
```
=> With address
```shell
address=123 Main St, Portland, OR 97204
```

## Request Body Sample (Batch Location API)
```json
{
    "locations": [
        {
            "lat": 45.6696163800542,
            "lng": -122.5038830583887,
            "correlationId": "123"
        },
        {
            "address": "123 Main St, Portland, OR 97204",
            "correlationId": "789"
        }
    ]
}
```

## Response Sample

=> Single Loation API
```json
{
    "data": [
        {
            "footprint_area_b": "2773.639996299906",
            "state_addr": "georgia",
            "roof_type_b": "mix",
            "roof_material_b": "shingle",
            "roof_condition_b": "good",
            "solar_panel_area_b": null,
            "air_conditioner_count_b": null,
            "ponding_area_b": null,
            "tarp_area_b": null,
            "pool_area_p": null,
            "number_of_floors_b": 4.0,
            "dis_tree_b": null,
            "dis_vegetation_b": null,
            "vegetation_zone_1_b": null,
            "vegetation_zone_2_b": null,
            "vegetation_zone_3_b": null,
            "vegetation_zone_4_b": null,
            "tree_zone_1_b": null,
            "tree_zone_2_b": null,
            "tree_zone_3_b": null,
            "tree_zone_4_b": null,
            "shrub_zone_1_b": null,
            "shrub_zone_2_b": null,
            "shrub_zone_3_b": null,
            "shrub_zone_4_b": null,
            "lon": null,
            "lat": null,
            "tree_overhang_b": null,
            "square_footage_b": 11094.559985199625,
            "addr": "1117 Whitehall Pointe, Sandy Springs, GA 30338",
            "roof_condition_prior_b": null,
            "roof_condition_prior_date_b": "2022-11-14"
        }
    ],
    "msg": "OK",
    "status": 200
}
```


=> Batch Location API
```json
{
    "data": [
        {
            "correlationId": "123",
            "results": [
                {
                    "footprint_area_b": "108.8532219525221",
                    "state_addr": "washington",
                    "roof_type_b": null,
                    "roof_material_b": "tile",
                    "roof_condition_b": "good",
                    "solar_panel_area_b": null,
                    "air_conditioner_count_b": null,
                    "ponding_area_b": null,
                    "tarp_area_b": null,
                    "pool_area_p": null,
                    "number_of_floors_b": null,
                    "dis_tree_b": "2.1292616370991424",
                    "dis_vegetation_b": "0.11043042611692858",
                    "vegetation_zone_1_b": "29",
                    "vegetation_zone_2_b": "16",
                    "vegetation_zone_3_b": "25",
                    "vegetation_zone_4_b": "22",
                    "tree_zone_1_b": "0",
                    "tree_zone_2_b": "0",
                    "tree_zone_3_b": "12",
                    "tree_zone_4_b": "7",
                    "shrub_zone_1_b": "29",
                    "shrub_zone_2_b": "16",
                    "shrub_zone_3_b": "13",
                    "shrub_zone_4_b": "15",
                    "lon": "-122.50387149798516",
                    "lat": "45.66950660751104",
                    "tree_overhang_b": null,
                    "square_footage_b": null,
                    "addr": "16317 Ne 65th St, Vancouver, WA 98682",
                    "roof_condition_prior_b": null,
                    "roof_condition_prior_date_b": "2022-11-19"
                },
                {
                    "footprint_area_b": "2108.8832754294",
                    "state_addr": "washington",
                    "roof_type_b": "gable",
                    "roof_material_b": "shingle",
                    "roof_condition_b": "good",
                    "solar_panel_area_b": "364.8951035416671",
                    "air_conditioner_count_b": null,
                    "ponding_area_b": null,
                    "tarp_area_b": null,
                    "pool_area_p": null,
                    "number_of_floors_b": null,
                    "dis_tree_b": "1.7108284542625607",
                    "dis_vegetation_b": "0.0",
                    "vegetation_zone_1_b": "23",
                    "vegetation_zone_2_b": "36",
                    "vegetation_zone_3_b": "20",
                    "vegetation_zone_4_b": "19",
                    "tree_zone_1_b": "0",
                    "tree_zone_2_b": "17",
                    "tree_zone_3_b": "8",
                    "tree_zone_4_b": "8",
                    "shrub_zone_1_b": "23",
                    "shrub_zone_2_b": "19",
                    "shrub_zone_3_b": "12",
                    "shrub_zone_4_b": "11",
                    "lon": "-122.5039519555898",
                    "lat": "45.669624440713015",
                    "tree_overhang_b": null,
                    "square_footage_b": null,
                    "addr": "16317 Ne 65th St, Vancouver, WA 98682",
                    "roof_condition_prior_b": null,
                    "roof_condition_prior_date_b": "2022-11-19"
                }
            ]
        },
        {
            "correlationId": "789",
            "results": [
                {
                    "footprint_area_b": "16562.97771247862",
                    "state_addr": "oregon",
                    "roof_type_b": "flat",
                    "roof_material_b": "concrete",
                    "roof_condition_b": "good",
                    "solar_panel_area_b": null,
                    "air_conditioner_count_b": "2",
                    "ponding_area_b": null,
                    "tarp_area_b": null,
                    "pool_area_p": null,
                    "number_of_floors_b": null,
                    "dis_tree_b": "2.1371682623582466",
                    "dis_vegetation_b": "2.1371682623582466",
                    "vegetation_zone_1_b": "0",
                    "vegetation_zone_2_b": "5",
                    "vegetation_zone_3_b": "12",
                    "vegetation_zone_4_b": "9",
                    "tree_zone_1_b": "0",
                    "tree_zone_2_b": "5",
                    "tree_zone_3_b": "10",
                    "tree_zone_4_b": "9",
                    "shrub_zone_1_b": "0",
                    "shrub_zone_2_b": "0",
                    "shrub_zone_3_b": "2",
                    "shrub_zone_4_b": "0",
                    "lon": "-122.67554249938121",
                    "lat": "45.51549869095183",
                    "tree_overhang_b": null,
                    "square_footage_b": null,
                    "addr": "101 Sw Main St, Portland, OR 97204",
                    "roof_condition_prior_b": null,
                    "roof_condition_prior_date_b": "2022-11-19"
                },
                {
                    "footprint_area_b": "1683.823485448854",
                    "state_addr": "oregon",
                    "roof_type_b": null,
                    "roof_material_b": "concrete",
                    "roof_condition_b": "good",
                    "solar_panel_area_b": null,
                    "air_conditioner_count_b": null,
                    "ponding_area_b": null,
                    "tarp_area_b": null,
                    "pool_area_p": null,
                    "number_of_floors_b": null,
                    "dis_tree_b": "0.3540499934188756",
                    "dis_vegetation_b": "0.0",
                    "vegetation_zone_1_b": "39",
                    "vegetation_zone_2_b": "29",
                    "vegetation_zone_3_b": "6",
                    "vegetation_zone_4_b": "5",
                    "tree_zone_1_b": "24",
                    "tree_zone_2_b": "26",
                    "tree_zone_3_b": "6",
                    "tree_zone_4_b": "5",
                    "shrub_zone_1_b": "15",
                    "shrub_zone_2_b": "3",
                    "shrub_zone_3_b": "0",
                    "shrub_zone_4_b": "0",
                    "lon": "-122.67502643731028",
                    "lat": "45.51561833767258",
                    "tree_overhang_b": null,
                    "square_footage_b": null,
                    "addr": "101 Sw Main St, Portland, OR 97204",
                    "roof_condition_prior_b": null,
                    "roof_condition_prior_date_b": "2022-11-19"
                }
            ]
        }
    ],
    "msg": "OK",
    "status": 200
}
```

# API Reference
Please visit this [link](https://api.geox-ai.com/api/v2/redoc) for more detailed description about API response attributes
