# Network Security on ESP32 through Amazon AWS IoT

In this project, students will connect their IoT kit to AWS IoT and use AWS IoT’s MQTT test client to check if the IoT kit can send messages to AWS IoT.

Please visit [AWS Free Tier](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc) to check free services from Amazon. For example, for 12 months following your initial sign-up date to AWS, you can send 250 K messages (published or delivered) per month for free with AWS IoT. Please ensure you do not send messages too fast and too many so as to avoid charge.

When you get into your AWS IoT portal as shown in Figure 1, click the Setting at the bottom left and you shall find your Custom endpoint, which works as a message broker/server. You can publish and subscribe to topics through your custom endpoint/broker. AWS IoT uses mutual authentication. In this assignment, Amazon will be a certificate authority (CA), create a public and privacy key pair and issue a certificate for our IoT kit, which also need Amazon’s CA certificate to authenticate the custom endpoint/broker. AWS IoT will keep the IoT kit’s certificate for the purpose of authenticating the IoT kit (called client too). This kind of client authentication is similar to the client authentication used by SSH.

Another important concept is the policy, which specifies who can use the custom endpoint for what. Please visit [AWS IoT Core policies](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policies.html). Below are MQTT Policy Actions.
-	*iot:Connect*: represents the permission to connect to the AWS IoT Core message broker.
-	*iot:Publish*: represents the permission to publish on an MQTT topic.
-	*iot:Receive*: represents the permission to receive a message from AWS IoT Core.
-	*iot:Subscribe*: represents the permission to subscribe to a topic filter.

## Create AWS account
Please visit [Set up your AWS account](https://docs.aws.amazon.com/iot/latest/developerguide/setting-up.html) to create your AWS account. Please visit [AWS account root user credentials and IAM user credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html) to see the difference between root user and IAM user. The root user can be used for this project to log into AWS console. 

## Create AWS IoT policy and Object
Please visit [Create AWS IoT resources](https://docs.aws.amazon.com/iot/latest/developerguide/create-iot-resources.html) and follow steps in this article.
-	Create an AWS IoT policy. Please provide screenshot of the created policy. (1 point)
-	Create a thing object. Please provide a screenshot of the listed keys and certificate which you can download. (1 point)
  -	Be sure to download the keys and certificate.
  -	You do not need to download the root CA file since we will have it from an example project.

## Download esp-aws-iot
Please visit [espressif/esp-aws-iot](https://github.com/espressif/esp-aws-iot). To use esp-aws-iot, we just need to open a terminal in the Ubuntu IoT VM and run the following commands to download the library and examples. 
-	cd /home/iot/esp
-	git clone --recursive https://github.com/espressif/esp-aws-iot

## Configure esp-aws-iot
Please visit [Amazon Web Services IoT Examples](https://github.com/espressif/esp-aws-iot/tree/master/examples). The student will work on the subscribe_publish example. Read Configuring Your Device carefully and use Option 1: Embedded Key & Cert into App Binary. 

Read *Installing Private Key & Certificate* at [Amazon Web Services IoT Examples](https://github.com/espressif/esp-aws-iot/tree/master/examples) carefully. Rename the downloaded private key and certificate accordingly and copy them to the appropriate folder of the subscribe_publish example downloaded to the Ubuntu IoT VM. 

Read *Find & Set AWS Endpoint Hostname* carefully. In this assignment, please use VS Code to configure the AWS endpoint hostname. That is, a student shall open the subscribe_publish example with VS Code and perform the configuration. Copy and paste your custom endpoint below. (1 point)

The subscribe_publish example requires the configuration of WiFi so that the IoT kit can connect to the Internet and AWS IoT. 

## Test esp-aws-iot
Please read [Amazon Web Services IoT MQTT Subscribe/Publish Example](https://github.com/espressif/esp-aws-iot/tree/master/examples/subscribe_publish) carefully. Note: the MQTT test client is located within Test at the bottom left of AWS IoT portal. For the use of the MQTT test client, please also visit [View MQTT messages with the AWS IoT MQTT client](https://docs.aws.amazon.com/iot/latest/developerguide/view-mqtt-messages.html). 

## Security analysis of esp-aws-iot
Please dump your IoT kit’s flash with esptool.py and search the flash for the private key of the IoT kit. The security implication is the private key of the IoT kit can be stolen from the dumped flash. Our IoT kit contains a crypto co-processor ATECC608A, which has an internal secure storage and hardware crypto acceleration. ATECC608A can be used to hold the private key, which cannot be extracted from outside of ATECC608A and does not leave the secure storage. All necessary crypto operations are done inside of ATECC608A. Therefore, the private key of the IoT kit can be protected. (1 point)

