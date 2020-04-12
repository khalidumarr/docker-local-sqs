# Docker Local SQS With AWS CLI
Docker Local SQS with AWS CLI pre-configured, This image is extension of *roribio16/alpine-sqs:latest*

## Installation
basic docker-compose.yml file has been added, can be used as a reference.

```
docker-compose up -d
```

## Create a queue
All AWS cli commands should work normally, Below is a sample command to make a new queue

```
docker-compose exec sqs aws --endpoint-url http://localhost:9324 sqs create-queue --queue-name test
```

Above command will return you following response:
```
{
    "QueueUrl": "http://localhost:9324/queue/test"
}
```
Now you can use this Queue URL in your other services, You can either use service name of
add network and assign alias to this service in order to be accessed by other services.

# Usage 
You can use below configurations in your projects with AWS SQS libraries to use from other services
```
KEY: notValidKey
SECRET: notValidSecret
REGION: eu-central-1
ENDPOINT: {ALIAS OR NAME OF SERVICE}:9324
QUEUE_URL: http://{ALIAS OR NAME OF SERVICE}:9324/queue/{QUEUE NAME}
```

## Example Usage
Below is example usage, in this case sqs service has been assigned a network alias _sqs.local_
```
KEY: notValidKey
SECRET: notValidSecret
REGION: eu-central-1
ENDPOINT: sqs.local:9324
QUEUE_URL: http://sqs.local:9324/queue/watto
```

## Things to Keep in mind While Using With Go Lang
You'll have to disable SSL, If you docker setup is not allowing http response to https
or you are getting error like "server gave HTTP response to HTTPS", then you can use below code as reference
```
awsConfig := aws.NewConfig().
		WithRegion({REGION}).
		WithCredentials(credentials.NewStaticCredentialsFromCreds(credentials.Value{
			AccessKeyID:     {KEY},
			SecretAccessKey: {SECRET},
		}))

	if devMode == true {
		awsConfig = awsConfig.
			WithEndpoint({ENDPOINT}).
			WithDisableSSL(true)
	}

	sess, err := session.NewSession(awsConfig)

```