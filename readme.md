#OddWorks on Heroku in 10 minutes

This is Work In Progress :D

##Prepare your repo

1. Fork oddworks to your github account, 
2. Clone your repo to your computer

```
git clone https://github.com/your-team/your-repo.git your-oddworks-project-name
```

##Setup Heroku
* Create an heroku app
* Add add-ons (mongo , elastic search and redis)

cd in the cloned repo and create an heroku app:

```
cd your-oddworks-project-name/
heroku apps:create your-app-name
heroku addons:create mongolab:sandbox
heroku addons:create heroku-redis:hobby-dev
```

heroku addons:create searchbox:starter will not work because of basic auth in the url

## Setup AWS Elastic Search
* create an iam user, 
* save credentials, 
* create a ES server, 
* allow iam user ARN

##Setup environment variables

If .env is in your .gitignore, setup environment variables in your heroku dashboard manually or using the command line:

some of them might not be needed:

```
heroku config:set NODE_ENV=staging
heroku config:set DOMAIN=your-app.herokuapp.com
heroku config:set PROTOCOL=http
heroku config:set PORT=80
heroku config:set ELASTICSEARCH_URI=url
heroku config:set AWS_ES_CREDS=username:password
heroku config:set AWS_DEFAULT_REGION=region
```

You don't need to set `REDIS_URL` or `MONGOLAB_URI` because it was already set when adding the add-ons. (you actually might need to set `REDIS_URI` because Heroku sets `REDIS_URL`) or modify config.js to search for `REDIS_URL` like so:

`const redisURL = process.env.REDIS_URI || process.env.REDIS_URL || 'redis://localhost:6379';`

If you want to setup OPBEAT, add these settings (you will obtain credentials while creating your OpBeat app)

```
heroku config:set OPBEAT_APP_ID=opbeatid
heroku config:set OPBEAT_ORGANIZATION_ID=opbeatidorg
heroku config:set OPBEAT_SECRET_TOKEN=opbeattoken
heroku config:set OPBEAT_LOG_LEVEL==info
```


###Alternative
If .env is not in your .gitignore, just push ot heroku. 

Copy the .env.sample to .env

```
cat .env.sample > .env
```

Edit .env with created credentials

```
vi .env
```
Set the node environment to `staging` or `production`, then get the configuration from the heroku variables info. Heroku creates environment variables so we don't have to populate them here (config.js will search for them)


`MONGOLAB_URI`, `REDIS_URL` (config.js actually looks for `REDIS_URI` so we need to write this one down)

Heroku searchly is using basic auth and oddworks doens't support parsing of basic auth, so we need to use something else


```
NODE_ENV=staging

DOMAIN=your-app.herokuapp.com
PROTOCOL=http
PORT=8000

REDIS_URL=xxxxx

OPBEAT_APP_ID=opbeatid
OPBEAT_ORGANIZATION_ID=opbeatidorg
OPBEAT_SECRET_TOKEN=opbeattoken
OPBEAT_LOG_LEVEL=

JWT_SHARED_SECRET=shhhhh
```


##Push to your repository

```
git add .
git commit -am "initial commit"
git push origin master
```

##Deploy to Heroku

```
git push heroku HEAD:master
```

##Check installation
To check if everything went fine, visit your heroku app url, and you should see a json response error: 

```
<?xml version='1.0' encoding='utf-8'?>
<response>
  <statusCode>400</statusCode>
  <error>Bad Request</error>
  <message>Invalid Access Token</message>
</response>
```

this is because we didn't use a token to make the API call.

## Get a token
go over at https://jwt.io/ and set the payload to match your platform:

/!\ missing correct payload, waiting for more info.

```
{
  "version": 1,
  "deviceID": "xxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxx",
  "scope": [
    "device"
  ],
  "iat": 1453932412
}
```

##TODO
* Next > Populate your catalog ( found this link on slack https://github.com/oddnetworks/sync-service)
* Next > Apple TV > compile framework

### References

* https://www.oddnetworks.com/documentation/oddworks/
* https://device-staging.oddworks.io
