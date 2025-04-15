# 📚 Serverless Student Management System

A fully serverless web application built on AWS that allows users to **add** and **retrieve** student records. The project uses AWS services like **S3**, **API Gateway**, **Lambda**, and **DynamoDB** to demonstrate modern, scalable, and cost-effective architecture.

---

## 📌 Features

- 📝 Add student information (name, ID, class, age)
- 🔍 View all stored student records
- ⚡ Serverless architecture (no server provisioning needed)
- ☁️ Deployed entirely on AWS using Free Tier services

---

## 🛠️ Tech Stack

| Layer    | Technology Used                      |
| -------- | ------------------------------------ |
| Frontend | HTML, CSS, JavaScript (hosted on S3) |
| API      | AWS API Gateway                      |
| Backend  | AWS Lambda (Python)                  |
| Database | AWS DynamoDB                         |
| IAM      | AWS IAM Roles & Policies             |

---

## 🧱 System Architecture

```

[Browser (User)]
|
v
[S3 Static Website Hosting]
|
v
[API Gateway (HTTP API)]
|
+--------------------+
|                    |
v                    v
[Lambda - POST] [Lambda - GET]
|                    |
v                    v
[Amazon DynamoDB - Student Records Table]

```

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/student-management-serverless.git
cd student-management-serverless
```

---

### 2. Frontend Setup (Static Site on S3)

1. Go to [S3 Console](https://s3.console.aws.amazon.com/s3/)
2. Create a new bucket (e.g., `student-management-app`)
3. Disable **Block Public Access** (for static hosting)
4. Upload the frontend files (`index.html`, `script.js`)
5. Enable **Static Website Hosting** and set `index.html` as the root document
6. Copy the S3 static website URL

---

### 3. DynamoDB Setup

1. Go to [DynamoDB Console](https://console.aws.amazon.com/dynamodb/)
2. Create a new table:
   - Table name: `studentData`
   - Partition key: `studentid` (String)
3. Choose **on-demand mode** (Free Tier friendly)

---

### 4. Create Lambda Functions

Create **two separate Lambda functions** with the following:

#### POST Lambda - Add Student

- **Code:**

```python
import json
import boto3
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('studentData')

def lambda_handler(event, context):
    body = json.loads(event['body'])
    table.put_item(Item={
        'studentid': body['studentid'],
        'name': body['name'],
        'class': body['class'],
        'age': body['age']
    })
    return {
        'statusCode': 200,
        'headers': { 'Access-Control-Allow-Origin': '*' },
        'body': json.dumps('Student data saved successfully!')
    }
```

#### GET Lambda - Fetch Students

```python
import json
import boto3
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('studentData')

def lambda_handler(event, context):
    response = table.scan()
    data = response['Items']
    while 'LastEvaluatedKey' in response:
        response = table.scan(ExclusiveStartKey=response['LastEvaluatedKey'])
        data.extend(response['Items'])
    return {
        'statusCode': 200,
        'headers': { 'Access-Control-Allow-Origin': '*' },
        'body': json.dumps(data)
    }
```

---

### 5. IAM Role & Permissions

- Attach the following policy to both Lambda functions:
  - `AmazonDynamoDBFullAccess`

---

### 6. Create API Gateway (HTTP API)

1. Go to [API Gateway Console](https://console.aws.amazon.com/apigateway/)
2. Create a **new HTTP API**
3. Add routes:
   - `POST /add` → POST Lambda
   - `GET /students` → GET Lambda
4. Deploy the API and copy the **Invoke URL**

---

### 7. Connect Frontend to API

Edit `script.js` to replace the API URLs:

```javascript
fetch('https://your-api-url/students')  // GET
fetch('https://your-api-url/add', {...})  // POST
```

---

## 🔒 CORS Setup

Enable CORS on both Lambda responses:

```python
'headers': {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Headers': '*',
    'Access-Control-Allow-Methods': 'GET,POST,OPTIONS'
}
```

---

## 🧹 Cleanup (Avoid Billing)

- Delete:
  - S3 bucket
  - DynamoDB table
  - Lambda functions
  - API Gateway
  - IAM roles (if unused)

---

## 📷 Screenshots

![Home](https://github.com/deveshanurag/Serverless-Computing/blob/main/home.png)

---

## 👨‍💻 Authors

- **Devesh Kumar**  
  Final Year, B.Tech CSE  
  PDPM IIITDM Jabalpur

---
