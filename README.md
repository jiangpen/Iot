# AWS Iot embedded C SDK usage tutorial
In this tutorial, it will show how to use AWS IoT embedded C SDK to perform mqtt shadow/job/publish.
##Download and Build SDK
- download SDK in your folder, saying iot, https://github.com/aws/aws-iot-device-sdk-embedded-C
- download mbedTLS and unzip the https://tls.mbed.org/download to folder iot/aws-iot-device-sdk-embedded-c/external_libs/mbedTLS
- go to aws console to create a IoT thing, https://docs.aws.amazon.com/iot/latest/developerguide/sdk-tutorials.html#iot-sdk-create-thing, and put the root CA, public key, private key file into the iot/aws-iot-device-sdk-embedded-c/certs
- in the iot/aws-iot-device-sdk-embedded-c/samples/linux/subscribe_publish_sample/aws_iot_config.h, and edit to the file as below:
```
#define AWS_IOT_MQTT_HOST              "xxxxxx.iot.ap-southeast-2.amazonaws.com" ///< Customer specific MQTT HOST. The same will be used for Thing Shadow
#define AWS_IOT_MQTT_PORT              443 ///< default port for MQTT/S
#define AWS_IOT_MQTT_CLIENT_ID         "your thing name " ///< MQTT client ID should be unique for every device
#define AWS_IOT_MY_THING_NAME 		   "your thing name" ///< Thing Name of the Shadow this device is associated with
#define AWS_IOT_ROOT_CA_FILENAME       "AmazonRootCA1.pem" ///< Root CA file name
#define AWS_IOT_CERTIFICATE_FILENAME   "certificate.pem.crt" ///< device signed certificate file name
#define AWS_IOT_PRIVATE_KEY_FILENAME   "private.pem.key" ///< Device private key filename
```
- change all other file in sample folder as same

#shadow
shadow is AWS service to maintain a status of the thing. It should be pubish state in mqtt retain mode. The shadow can be access anytime event thing is not online. So anything to update shadow must include a timestamp. 
run iot/aws-iot-device-sdk-embedded-c/samples/linux/shadow_sample/shadow_sample, you can log into AWS console to see the shadow gets update.
Also can check the shadow from AWS CLI as below
```
aws iot-data get-thing-shadow --thing-name mythingname output.txt
```
#job
Job is to send command/config to IoT device. Run iot/aws-iot-device-sdk-embedded-c/samples/linux/jobs_sample/jobs_sample. This example will wait for a job.
Sending a job from the AWS CLI to test.
create a json file named example-job.json

```
{
    "operation":"customJob",
    "otherInfo":"someValue",
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::mybucket/*"
        }
    ]
}

aws iot create-job  --job-id "example-job-xx"  --targets "arn:find your thing arn in aws console"  --document file://./example-job.json  --description "My First test job"  --target-selection SNAPSHOT
```
You can see you thing receive this job request. In this example, a S3 file download request can be sent.

