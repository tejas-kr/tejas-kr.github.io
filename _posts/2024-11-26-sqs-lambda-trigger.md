---
layout: post
category: technical
custom_js: mouse_coords
---

# Trigger a Lambda Function on SQS Message

## The Basic Knowledge

To trigger an AWS Lambda function when a message is sent to an Amazon SQS queue, follow these steps:

---

### **1. Create an SQS Queue**

1. Go to the [Amazon SQS Console](https://console.aws.amazon.com/sqs/).
2. Click **Create Queue** and configure your queue (Standard or FIFO).
3. Note the queue URL.

---

### **2. Create or Use an Existing Lambda Function**

1. Navigate to the [Lambda Console](https://console.aws.amazon.com/lambda/).
2. Create a new Lambda function or use an existing one.
3. Write the code to process the SQS message. For example, in Python:

```python
import json

def lambda_handler(event, context):
    for record in event['Records']:
        # Extract the message body
        message_body = record['body']
        print(f"Received message: {message_body}")
        # Process the message as needed
    return {"statusCode": 200, "body": "Processed messages successfully"}
```

---

### **3. Add the SQS Trigger to Lambda**

1. In the Lambda console, select your function.
2. In the **Function Overview** section, click **Add Trigger**.
3. Select **SQS** as the source and choose your queue from the dropdown.
4. Configure the batch size (number of messages processed at once) and enable the trigger.

---

### **4. Set Permissions**

Lambda needs permission to read messages from the SQS queue. This is usually handled automatically when you attach the SQS trigger. If you need to add permissions manually:

1. Go to the **IAM Console**.
2. Find the execution role associated with your Lambda function.
3. Attach a policy allowing `sqs:ReceiveMessage` and `sqs:DeleteMessage` actions for your queue.

Example policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "sqs:ReceiveMessage",
                "sqs:DeleteMessage",
                "sqs:GetQueueAttributes"
            ],
            "Resource": "arn:aws:sqs:<region>:<account-id>:<queue-name>"
        }
    ]
}
```

---

### **5. Test the Setup**

1. Send a test message to your SQS queue using the AWS CLI, SDK, or Console.
    - Example using AWS CLI:
        
        ```bash
        aws sqs send-message --queue-url <queue-url> --message-body "Hello from SQS"
        ```
        
2. Check the Lambda logs in **CloudWatch Logs** to verify the message was processed.

---

### **6. Optional Configurations**

- **Dead Letter Queue (DLQ):** Configure a DLQ to handle failed message processing.
- **Retries and Timeout:** Adjust these settings in the Lambda function's configuration as needed.

## A Yaml CloudFormation Template for provisioning all the necessary resources

Here’s a CloudFormation YAML template to create an SQS queue, a Lambda function, and the necessary IAM roles and permissions to connect them.

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: CloudFormation template to create an SQS queue, Lambda function, and necessary permissions.

Resources:
  # SQS Queue
  MyQueue:
    Type: "AWS::SQS::Queue"
    Properties:
      QueueName: "MyQueue"

  # Lambda Execution Role
  LambdaExecutionRole:
    Type: "AWS::IAM::Role"
    Properties:
      RoleName: "LambdaSQSExecutionRole"
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              Service:
                - "lambda.amazonaws.com"
            Action: "sts:AssumeRole"
      Policies:
        - PolicyName: "LambdaSQSPolicy"
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: "Allow"
                Action:
                  - "sqs:ReceiveMessage"
                  - "sqs:DeleteMessage"
                  - "sqs:GetQueueAttributes"
                Resource: !GetAtt MyQueue.Arn
              - Effect: "Allow"
                Action:
                  - "logs:CreateLogGroup"
                  - "logs:CreateLogStream"
                  - "logs:PutLogEvents"
                Resource: "arn:aws:logs:*:*:*"

  # Lambda Function
  MyLambdaFunction:
    Type: "AWS::Lambda::Function"
    Properties:
      FunctionName: "ProcessSQSMessages"
      Runtime: "python3.9"
      Handler: "index.lambda_handler"
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        ZipFile: |
          import json
          def lambda_handler(event, context):
              for record in event['Records']:
                  print(f"Message Body: {record['body']}")
              return {"statusCode": 200, "body": "Messages processed"}

      Timeout: 10

  # Lambda Permission to Allow SQS Trigger
  LambdaPermissionForSQS:
    Type: "AWS::Lambda::Permission"
    Properties:
      FunctionName: !Ref MyLambdaFunction
      Action: "lambda:InvokeFunction"
      Principal: "sqs.amazonaws.com"
      SourceArn: !GetAtt MyQueue.Arn

  # SQS Event Source Mapping
  SQSToLambdaEventSource:
    Type: "AWS::Lambda::EventSourceMapping"
    Properties:
      EventSourceArn: !GetAtt MyQueue.Arn
      FunctionName: !Ref MyLambdaFunction
      BatchSize: 10
      Enabled: True

Outputs:
  SQSQueueURL:
    Value: !Ref MyQueue
    Description: "URL of the created SQS queue"

  LambdaFunctionName:
    Value: !Ref MyLambdaFunction
    Description: "Name of the created Lambda function"
```

### Key Sections:

1. **SQS Queue:** Creates the SQS queue (`MyQueue`).
2. **IAM Role:** Grants the Lambda function permission to interact with SQS and CloudWatch Logs.
3. **Lambda Function:** A simple Python-based function that processes SQS messages.
4. **Event Source Mapping:** Connects the Lambda function to the SQS queue for automatic invocation on new messages.
5. **Lambda Permission:** Allows SQS to invoke the Lambda function.

### Deployment Steps:

1. Save the YAML to a file, e.g., `sqs-lambda.yml`.
2. Use the AWS CLI or AWS Management Console to deploy the stack:
    - CLI:
        
        ```bash
        aws cloudformation create-stack --stack-name sqs-lambda-stack --template-body file://sqs-lambda.yml --capabilities CAPABILITY_NAMED_IAM
        ```
        
3. Once deployed, send a message to the SQS queue and check the Lambda execution logs in CloudWatch.

## Summary of the things you need to do.

Here’s a summary of the steps you need to take to set up an SQS-triggered Lambda function using AWS CloudFormation:

---

### **1. Write the CloudFormation YAML File**

- Create a CloudFormation template containing:
    - **SQS Queue**: To queue messages.
    - **Lambda Function**: To process the messages.
    - **IAM Role**: To grant Lambda permissions for interacting with SQS and CloudWatch Logs.
    - **Event Source Mapping**: To link the SQS queue to the Lambda function.
    - **Lambda Permission**: To allow SQS to invoke the Lambda function.

---

### **2. Deploy the CloudFormation Stack**

1. Save the template file (e.g., `sqs-lambda.yml`).
2. Deploy it using the AWS CLI:
    
    ```bash
    aws cloudformation create-stack --stack-name sqs-lambda-stack --template-body file://sqs-lambda.yml --capabilities CAPABILITY_NAMED_IAM
    ```
    
    Or upload it in the **AWS Management Console** under CloudFormation.

---

### **3. Test the Setup**

1. Send a message to the SQS queue:
    
    ```bash
    aws sqs send-message --queue-url <Queue-URL> --message-body "Hello from SQS"
    ```
    
2. Check CloudWatch Logs to verify that the Lambda function processed the message.

---

### **4. Monitor and Adjust as Needed**

- Adjust the **batch size** or **timeout** of the Lambda function if required.
- Optionally, configure a **Dead Letter Queue (DLQ)** for handling failed messages.

---
