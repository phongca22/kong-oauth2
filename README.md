# OAuth 2.0 Authentication
Add an OAuth 2.0 authentication layer with the Resource Owner Password Credentials Grant flow.

## Create a Service

``` bash
curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=google-svc' \
  --data 'url=http://google.com'
```

## Create a Route

``` bash
curl -i -X POST \
  --url http://localhost:8001/services/google-svc/routes \
  --data 'hosts[]=localhost' \
  --data 'methods[]=GET&methods[]=POST'
```

## Test

``` bash
curl -i -X GET \
  --url http://localhost:8000/ \
```

You will see Google website content


## Create a Consumer

``` bash
curl -X POST http://localhost:8001/consumers/ \
    --data "username=phong" \
    --data "custom_id=phong_id"
```
The response will return the Consumer Id, you should note it.
Example: id = 01b28940-7ece-4179-8b58-73b731d8e607

## Create an Application
Use Consumer ID above
``` bash
curl -X POST http://localhost:8001/consumers/01b28940-7ece-4179-8b58-73b731d8e607/oauth2 \
    --data "name=oauth-2-app" \
    --data "client_id=cid" \
    --data "client_secret=cserect" \
    --data "redirect_uri=http://google.com"
```
