# Network Security on ESP32 through Amazon AWS IoT

In this project, students will connect their IoT kit to AWS IoT and use AWS IoT’s MQTT test client to check if the IoT kit can send messages to AWS IoT.

Please visit [AWS Free Tier](https://aws.amazon.com/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc) to check free services from Amazon. For example, for 12 months following your initial sign-up date to AWS, you can send 250 K messages (published or delivered) per month for free with AWS IoT. Please ensure you do not send messages too fast and too many so as to avoid charge.

When you get into your AWS IoT portal as shown below, click the *Setting* at the bottom left and you shall find your custom *Endpoint*, which works as a message broker/server. You can publish and subscribe to topics through your custom endpoint/broker. AWS IoT uses mutual authentication. In this project, Amazon will be a certificate authority (CA), create a public and privacy key pair and issue a certificate for our IoT kit, which also need Amazon’s CA certificate to authenticate the custom endpoint/broker. AWS IoT will keep the IoT kit’s certificate for the purpose of authenticating the IoT kit (called client too).

<img src="https://github.com/xinwenfu/Network-Security-on-ESP32/assets/69218457/d0fceb5b-3e8a-48f6-b303-40534b515c18" width=1080>

Another important concept is the policy, which specifies who can use the custom endpoint for what. Please visit [AWS IoT Core policies](https://docs.aws.amazon.com/iot/latest/developerguide/iot-policies.html). Below are MQTT Policy Actions.
-	*iot:Connect*: permission to connect to the AWS IoT Core message broker.
-	*iot:Publish*: permission to publish on an MQTT topic.
-	*iot:Receive*: permission to receive a message from AWS IoT Core.
-	*iot:Subscribe*: permission to subscribe to a topic filter.

## 1. Create AWS account
Please visit [Set up your AWS account](https://docs.aws.amazon.com/iot/latest/developerguide/setting-up.html) to create your AWS account. Please visit [AWS account root user credentials and IAM user credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html) to see the difference between root user and IAM user. The root user can be used for this project to log into AWS console. 

## 2. Create AWS IoT policy and Object
Please visit [Create AWS IoT resources](https://docs.aws.amazon.com/iot/latest/developerguide/create-iot-resources.html) and follow steps in this article.
-	Create an AWS IoT policy
-	Create a thing object
  -	Be sure to download the key and certificate for the thing.
  -	You do not need to download the root CA file since we will have it from an example project.

## 3. (Optional) Download esp-aws-iot
The project is already available at */home/iot/esp/IoT-Examples/espidf-aws-iot*.

If needed, please visit [espressif/esp-aws-iot](https://github.com/espressif/esp-aws-iot). To use esp-aws-iot, we just need to open a terminal in the Ubuntu IoT VM and run the following commands to download the library and examples. 
-	cd /home/iot/esp
-	git clone -b release/v3.1.x --recursive https://github.com/espressif/esp-aws-iot

## 4. Configure esp-aws-iot
Please visit [Amazon Web Services IoT Examples](https://github.com/espressif/esp-aws-iot/tree/release/v3.1.x/examples). The student will work on the subscribe_publish example. Read Configuring Your Device carefully and use Option 1: Embedded Key & Cert into App Binary. 

- Read *Installing Private Key & Certificate* at [Amazon Web Services IoT Examples](https://github.com/espressif/esp-aws-iot/tree/release/v3.1.x/examples) carefully. Rename the downloaded private key and certificate accordingly and copy them to the appropriate folder of the subscribe_publish example downloaded to the Ubuntu IoT VM. 

- Read *Find & Set AWS Endpoint Hostname* carefully. In this project, please use VS Code to configure the AWS endpoint hostname. That is, a student shall open the subscribe_publish example with VS Code and perform the configuration.

- The subscribe_publish example requires the configuration of WiFi so that the IoT kit can connect to the Internet and AWS IoT. 

## 5. Test esp-aws-iot
Please read [Amazon Web Services IoT MQTT Subscribe/Publish Example](https://github.com/espressif/esp-aws-iot/tree/release/v3.1.x/examples/subscribe_publish) carefully. The *MQTT test client* is located within Test at the left of AWS IoT portal. For the use of the MQTT test client, please also visit [View MQTT messages with the AWS IoT MQTT client](https://docs.aws.amazon.com/iot/latest/developerguide/view-mqtt-messages.html). 


## 6. Create DynamoDB table and AWS IoT rule 
Please read carefully [Store device data in a DynamoDB table](https://docs.aws.amazon.com/iot/latest/developerguide/iot-ddb-rule.html). We will save AHT readings sent from our IoT kit into a DynamoDB table. Note our AHT sensor has two readings: temperature and humidity.
- Create a DynamoDB table
- Create an AWS IoT rule that forwards the AHT sensor data into the DynamoDB table. 

## 7. Save DHT22 Data into DynamoDB of AWS
Please read [Get started with ESP32 via VS Code and the ESP-IDF extension](https://github.com/xinwenfu/tst-dht-lab--pcb-2) and understand how to read the AHT sensor data.
Carefully read *subscribe_publish_sample.c* of this project and find where data is sent to AWS IoT. 
Merge the code of reading data from the AHT sensor into this project so that the AHT sensor data can be sent to AWS IoT.

The AHT reading has to be formatted in the JSON format in C so that it can work with the AWS IoT rules. Please refer to the example below. Note our AHT sensor has two readings: temperature and humidity.
```
sprintf(cPayload, "{\"temperature\": 28, \"humidity\": 80}");
```

## 8. (Optional) Security analysis of esp-aws-iot
Please dump your IoT kit’s flash with esptool.py and search the flash for the private key of the IoT kit. The security implication is the private key of the IoT kit can be stolen from the dumped flash. Our IoT kit contains a crypto co-processor ATECC608A, which has an internal secure storage and hardware crypto acceleration. ATECC608A can be used to hold the private key, which cannot be extracted from outside of ATECC608A and does not leave the secure storage. All necessary crypto operations are done inside of ATECC608A. Therefore, the private key of the IoT kit can be protected.

