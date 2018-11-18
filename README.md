# OAuth 2.0 Authentication
Add an OAuth 2.0 authentication layer with the Resource Owner Password Credentials Grant flow.

## Create a Service

``` bash
curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=google-svc' \
  --data 'url=http://google.com'
```
Get `service_id` in the response.

## Create a Route
Replace `service_id` in `/services/{service_id}/routes`

``` bash
curl -i -X POST \
  --url http://localhost:8001/services/google-svc/routes \
  --data 'hosts[]=localhost' \
  --data 'methods[]=GET&methods[]=POST'
```

Get `route_id` in the response.

## Test

``` bash
curl -i -X GET \
  --url http://localhost:8000/
```

We will see Google website content


## Create a Consumer

``` bash
curl -X POST http://localhost:8001/consumers/ \
    --data "username=phong" \
    --data "custom_id=phong_id"
```
Get `consumer_id` in the response.

## Create an Application
Replace `consumer_id` in `/consumers/{consumer_id}/oauth2`.

``` bash
curl -X POST http://localhost:8001/consumers/01b28940-7ece-4179-8b58-73b731d8e607/oauth2 \
    --data "name=oauth-2-app" \
    --data "client_id=cid" \
    --data "client_secret=cserect" \
    --data "redirect_uri=http://google.com"
```

We have `client_id` and `client_secret`.

## Enabling the plugin on a Service
Replace `service_id` in `/services/{service_id}/plugins`.
``` bash
curl -X POST http://localhost:8001/services/google-svc/plugins \
    --data "name=oauth2"  \
    --data "config.scopes=email,read" \
    --data "config.mandatory_scope=true" \
    --data "config.enable_password_grant=true"
```

Get `provision_key` in the response.

## Get token
Parameters:
* `authenticated_userid` = `custom_id`

``` bash
curl -k https://localhost:8443/google-svc/oauth2/token \
     --data "client_id=cid" \
     --data "client_secret=cserect" \
     --data "grant_type=password" \
     --data "scope=read" \
     --data "provision_key=kDNBwCVrTjQU5cLQI2FDyI6onAYpAIBI" \
     --data "authenticated_userid=phong_id"
```

We have `access_token` and `refesh_token`.

## Test without oauth2
``` bash
curl -i -X GET \
  --url http://localhost:8000/
```

Error
``` json
{
    "error_description": "The access token is missing",
    "error": "invalid_request"
}
```

## Test with oauth2

Replace `access_token` in `Authorization: Bearer {access_token}`.
``` bash
curl -i -X GET \
  --url http://localhost:8000/ \
  --header "Authorization: Bearer a503faf9-45b5-4fec-8334-337284a66ea4"
```

We will see Google website content again.

## Refresh token
Replace `refresh_token` in `--data "refresh_token={refresh_token}"`.
``` bash
curl -k POST https://localhost:8443/google-svc/oauth2/token \
    --data "grant_type=refresh_token" \
    --data "client_id=ci" \
    --data "client_secret=csecret" \
    --data "refresh_token=a503faf9-45b5-4fec-8334-337284a66ea4"
```
     
     

