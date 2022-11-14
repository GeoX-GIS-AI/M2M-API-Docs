# Overview
This API allows you to search properties from our vast range of footprints data. 

## Endpoint URL
The base URL for the API is
```bash
https://api.geox-ai.com
```

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
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.geox-ai.com/api/v8.3/parcels
3. Select the POST HTTP method
4. Setup authorization as mentioned above.
5. Now put your lat/lng in the Params section with `lat` and `lng` keys.
6. Finally, hit the API.

## Hitting API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth
### Python code for API Call with Post Method
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def m2m_request(access_key, secret_key, lat, lng):
    api_url = "https://api.geox-ai.com/api/v8.3/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.geox-ai.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    request_params = {
        "lat": lat,
        "lng": lng
    }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    m2m_response = m2m_request("YOUR_API_KEY", "YOUR_API_SECRET",
                               45.6696163800542, -122.5038830583887)
    print(json.dumps(m2m_response, indent=2))
```

## Hitting API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) for more details. 
```shell
curl --location --request GET 'https://api.geox-ai.com/api/v8.3/parcels?lat=33.970191989230656&lng=-84.34141973584119' \
--header 'X-Amz-Date: 20221114T085525Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221114/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=9297d31a62bdfd96c3d78a3a98a652b8270b80d02b9c2252a9d0a2d8a4e73c6e'
```

## Hitting API with wget
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20221114T085525Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221114/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=9297d31a62bdfd96c3d78a3a98a652b8270b80d02b9c2252a9d0a2d8a4e73c6e' \
   'https://api.geox-ai.com/api/v8.3/parcels?lat=33.970191989230656&lng=-84.34141973584119'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.geox-ai.com/api/v8.3/parcels
```

## Request Query params Sample
```shell
lat=33.970191989230656&lng=-84.34141973584119
```

## Response Sample
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

# API Reference
Please visit this [link](https://api.geox-ai.com/api/v2/redoc) for more detailed description about API response attributes
