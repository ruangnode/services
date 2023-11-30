# AWS lambda executor setup

## Setting Up Yoda with Lambda Executor

Yoda uses an external executor to resolve requests to data sources. Currently, it supports AWS Lambda (through REST interface). In future releases, yoda will support more executors and allow you to specify multiple executors to add redundancy.

### Creating Lambda Function

In your AWS account, go to [AWS Lambda Page](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) and click **Create function**.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Follow the following steps:

* Select **Author from Scratch**
* Choose your **Function name** It will be your endpoint route.
* Set **Runtime** to `Python 3.7`
* For **permission**, if you don't have an existing role, create a new one.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Click **Create function**. Once everything is complete, You will see this page.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Scroll down to Function code panel and click Actions -> Upload a `.zip` file. You will need to upload [the runtime zip](https://github.com/bandprotocol/data-source-runtime/releases/download/v2.0.0/lambda-yoda.zip).

<figure><img src="../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Go to **Configuration - Environment variabeles - Edit**\
**Environment variables** section and add 2 environment variables: `MAX_EXECUTABLE` to 8192 (8 MB) and `MAX_DATA_SIZE` to 256.

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

Go to **Configuration - General Configuration - Edit** \
**Basic Settings** and update the runtime configurations. We recommend using 512MB RAM and 12 seconds timeout.

<figure><img src="../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

We will use **API Gateway** for receiving a request from our Yoda program. Let’s create a new trigger by clicking **+ Add trigger** -> **API Gateway** and follow the setup wizard to create a new API endpoint connecting to your Lambda function.

<figure><img src="../../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

Once completed, you will see the **API endpoint** that will be used as the endpoint URL to test your Lambda function.

You can now test the endpoint using `curl`

```
curl --location --request POST '<your_api_endpoint>' \
--header 'Content-Type: application/json' \
--data-raw '{
    "executable": "IyEvdXNyL2Jpbi9lbnYgcHl0aG9uMwoKaW1wb3J0IHN5cwoKZGVmIG1haW4oZGF0YSk6CiAgICByZXR1cm4gZGF0YQoKCmlmIF9fbmFtZV9fID09ICJfX21haW5fXyI6CiAgICB0cnk6CiAgICAgICAgcHJpbnQobWFpbigqc3lzLmFyZ3ZbMTpdKSkKICAgIGV4Y2VwdCBFeGNlcHRpb24gYXMgZToKICAgICAgICBwcmludChzdHIoZSksIGZpbGU9c3lzLnN0ZGVycikKICAgICAgICBzeXMuZXhpdCgxKQo=",
    "calldata": "\"Hello lambda\"",
    "timeout": 3000
}'
```

The expected result should be:

```
{
    "returncode": 0,
    "stdout": "Hello lambda\n",
    "stderr": "",
    "error": "",
    "version": "lambda:1.1.2"
}
```

Go to Active Oracle session click here [Setup Yoda](set-the-yoda-configurations.md#step-52-set-the-yoda-configurations)
