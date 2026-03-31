# aws-sqs-messaging-project
AWS Simple SQS Messaging System with Python &amp; AWS CLI

sudo yum install git -y 
sudo yum install python3-pip  
pip install boto3

git clone https://github.com/atulkamble/aws-sqs-messaging-project.git
cd aws-sqs-messaging-project

chmod +x create_queue.sh
./create_queue.sh

python3 send_message.py

python3 receive_message.py

chmod +x delete_queue.sh
./delete_queue.sh

🎯 Objective
Build a simple messaging system using AWS SQS that:

Creates a Standard Queue
Sends a message
Retrieves the message
Deletes the message
Deletes the queue
📋 Prerequisites
💻 Local Setup
Python 3.x
Boto3 library
AWS CLI installed & configured
🔐 AWS Setup
An IAM user with AmazonSQSFullAccess
Access key and secret configured via aws configure

 1. Install Python & AWS CLI
✅ Check Python
python --version
python3 --version
🪟 Install Python (Windows - PowerShell Admin)
choco install python
✅ Check AWS CLI
aws --version
If not installed, follow https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html

🔐 2. IAM User Setup
Go to AWS Console → IAM → Users → Add user

Username: atul

Access Type: ✅ Programmatic access

Add to Group: admin with AdministratorAccess or AmazonSQSFullAccess

Download .csv file with:

Access Key ID
Secret Access Key
⚙️ 3. Configure AWS CLI
aws configure
Enter the following when prompted:

Access Key ID
Secret Access Key
Region: us-east-1
Output Format: json
Verify:

aws s3 ls
💻 4. EC2 Setup (Optional Remote Execution)
SSH into EC2 (Amazon Linux):

ssh -i <your-key.pem> ec2-user@<ec2-ip>
Install required packages:

sudo yum install git python3 -y
sudo yum install python3-pip -y
pip3 install boto3
📁 5. Project Structure
aws-sqs-messaging-project/
│
├── create_queue.sh
├── send_message.py
├── receive_message.py
├── delete_message.py
├── delete_queue.sh
└── requirements.txt
🧱 6. Project Code
🪄 create_queue.sh
#!/bin/bash

QUEUE_NAME="MyTestQueue"

aws sqs create-queue \
  --queue-name $QUEUE_NAME \
  --attributes VisibilityTimeout=60

echo "✅ Queue $QUEUE_NAME created successfully."
📦 requirements.txt
boto3
Install it:

pip3 install -r requirements.txt
Or check manually:

pip3 show boto3
pip3 install boto3
✉️ send_message.py
import boto3

queue_name = "MyTestQueue"
sqs = boto3.client('sqs')

queue_url = sqs.get_queue_url(QueueName=queue_name)['QueueUrl']

response = sqs.send_message(
    QueueUrl=queue_url,
    MessageBody="Hello from Cloudnautic SQS Project!"
)

print("✅ Message sent!")
print("🆔 Message ID:", response['MessageId'])
📥 receive_message.py
import boto3
import json

queue_name = "MyTestQueue"
sqs = boto3.client('sqs')

queue_url = sqs.get_queue_url(QueueName=queue_name)['QueueUrl']

response = sqs.receive_message(
    QueueUrl=queue_url,
    MaxNumberOfMessages=1,
    WaitTimeSeconds=5
)

messages = response.get('Messages', [])
if not messages:
    print("📭 No messages in queue.")
else:
    for msg in messages:
        print("📨 Received:", msg['Body'])
        print("🧾 ReceiptHandle:", msg['ReceiptHandle'])

        with open('last_receipt_handle.json', 'w') as f:
            json.dump({'ReceiptHandle': msg['ReceiptHandle']}, f)
🗑️ delete_message.py
import boto3
import json
import os

queue_name = "MyTestQueue"
sqs = boto3.client('sqs')

queue_url = sqs.get_queue_url(QueueName=queue_name)['QueueUrl']

if not os.path.exists('last_receipt_handle.json'):
    print("❌ No receipt handle found. Run receive_message.py first.")
    exit()

with open('last_receipt_handle.json') as f:
    receipt_data = json.load(f)

receipt_handle = receipt_data['ReceiptHandle']

sqs.delete_message(
    QueueUrl=queue_url,
    ReceiptHandle=receipt_handle
)

print("🗑️ Message deleted successfully.")
🧹 delete_queue.sh
#!/bin/bash

QUEUE_NAME="MyTestQueue"

QUEUE_URL=$(aws sqs get-queue-url --queue-name "$QUEUE_NAME" --query 'QueueUrl' --output text)

aws sqs delete-queue --queue-url "$QUEUE_URL"

echo "🗑️ Queue $QUEUE_NAME deleted successfully."
▶️ 7. How to Run This Project
# Clone the repository
git clone https://github.com/atulkamble/aws-sqs-messaging-project.git
cd aws-sqs-messaging-project

# Step 1: Create the Queue
bash create_queue.sh

# Step 2: Install Dependencies
pip3 install -r requirements.txt

# Step 3: Send a message
python3 send_message.py

# Step 4: Receive the message
python3 receive_message.py

# Step 5: Delete the message
python3 delete_message.py

# Step 6: Delete the Queue
bash delete_queue.sh
✅ Expected Output
Step	Output
Create Queue	✅ Queue created successfully
Send Message	✅ Message sent! with 🆔 Message ID
Receive Msg	📨 Message Body + 🧾 ReceiptHandle
Delete Msg	🗑️ Message deleted successfully
Delete Queue	🗑️ Queue deleted successfully
