# Cloud Native Resource Monitoring Python App on K8s
 -------------------------------------------
 
## Things you will Learn 

1. Python and How to create Monitoring Application in Python using Flask and psutil
2. How to run a Python App locally.
3. Learn Docker and How to containerize a Python application
    1. Creating Dockerfile
    2. Building DockerImage
    3. Running Docker Container
    4. Docker Commands
4. Create ECR repository using Python Boto3 and pushing Docker Image to ECR
5. Learn Kubernetes and Create EKS cluster and Nodegroups
6. Create Kubernetes Deployments and Services using Python!

#  For step by step Demonstration !

```
```
![monitoring](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/a82cdd9c-b61f-49e9-88aa-db6ec06c2a79)

```
```
## Prerequisites !

(Things to have before starting the projects)

- [x]  AWS Account.
- [x]  Programmatic access and AWS configured with CLI.
- [x]  Python3 Installed.
- [x]  Docker and Kubectl installed.
- [x]  Code editor (Vscode)

# Let’s Start the Project 

## Part 1: Deploying the Flask application locally

### Step 1: Clone the code

Clone the code from the repository:

```
git clone <repository_url>
```

### Step 2: Install dependencies

The application uses the **`psutil`** and **`Flask`, Plotly, boto3** libraries. Install them using pip:

```
pip3 install -r requirements.txt
```

### Step 3: Run the application

To run the application, navigate to the root directory of the project and execute the following command:

```
python3 app.py
```

This will start the Flask server on **`localhost:5000`**. Navigate to [http://localhost:5000/](http://localhost:5000/) on your browser to access the application.

## Part 2: Dockerizing the Flask application

### Step 1: Create a Dockerfile

Create a **`Dockerfile`** in the root directory of the project with the following contents:

```
# Use the official Python image as the base image
FROM python:3.9-slim-buster

# Set the working directory in the container
WORKDIR /app

# Copy the requirements file to the working directory
COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

# Copy the application code to the working directory
COPY . .

# Set the environment variables for the Flask app
ENV FLASK_RUN_HOST=0.0.0.0

# Expose the port on which the Flask app will run
EXPOSE 5000

# Start the Flask app when the container is run
CMD ["flask", "run"]
```

### Step 2: Build the Docker image

To build the Docker image, execute the following command:

```
docker build -t <image_name> .
```

### Step 3: Run the Docker container

To run the Docker container, execute the following command:

```
docker run -p 5000:5000 <image_name>
```

This will start the Flask server in a Docker container on **`localhost:5000`**. Navigate to [http://localhost:5000/](http://localhost:5000/) on your browser to access the application.
```
```
![Screenshot 2024-06-10 163024](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/0abf2c26-871b-4c45-90b8-c5cf002904d5)
```
```
## Part 3: Pushing the Docker image to ECR

### Step 1: Create an ECR repository

To set up an AWS ECR repository, build a Docker image, and push it to the repository, you can use the following Bash script:

```bash
#!/bin/bash

# Variables
REGION="your-region"
REPO_NAME="your-repo-name"
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# Create ECR repository
aws ecr create-repository --repository-name $REPO_NAME --region $REGION

# Login to ECR
aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com
```
### Step 2: Push the Docker image to ECR

Push the Docker image to ECR using the push commands on the console:

```
docker push <ecr_repo_uri>:<tag>
```

## Part 4: Creating an EKS cluster and deploying the app using Python
                  
### Step 1: Create VPC using CloudFormation in AWS Console
Set Up VPC Using Console:

In AWS console, create VPC, subnet, internet gateway, and route table.

### Step 2: Create an EKS cluster

Create an EKS cluster and add node group 
```
```
![Screenshot 2024-06-11 192028](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/f2c798fb-9c54-4056-9dc0-13cd7d9c30cd)
```
```

### Step 3: Create a node group

Create a node group in the EKS cluster.
```
```
![Screenshot 2024-06-11 230052](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/afdebc71-87ba-4e71-8c02-0d3f7a83a6ac)
```
```
### Step 4: Create namespace deployment and service
# Deployment Instructions

## YAML Configuration

To create the namespace, deployment, and service, use the following YAML configuration:

```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudnative-monitoringapp-deployment
  namespace: my-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: cloudnative-monitoringapp
  template:
    metadata:
      labels:
        app: cloudnative-monitoringapp
    spec:
      containers:
      - name: cloudnative-monitoringapp
        image: public.ecr.aws/v9e0r7f8/cloudnative-monitoringapp:latest
        ports:
        - containerPort: 5000

---
apiVersion: v1
kind: Service
metadata:
  name: cloudnative-monitoringapp-service
  namespace: my-namespace
spec:
  type: LoadBalancer
  selector:
    app: cloudnative-monitoringapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```
```
kubectl get deployment -n my-namespace (check deployments)
kubectl get service -n my-namespace (check service)
kubectl get pods -n my-namespace (to check the pods)
```

![Screenshot 2024-06-11 224829](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/0982a0e3-c207-4f56-b123-63d2d40530d9)

Once your pod is up and running,

![Screenshot 2024-06-11 230504](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/d5726ef2-36dc-4b4a-959e-616eceac0de7)

This will start the Flask server in a EKS cluster .Use ElasticLoadBalancer end point http://a73cbda19311d4617bacce53d6230831-1956369547.ap-south-1.elb.amazonaws.com on your browser to access the application.

This should direct you to your cloudnative-monitoringapp application running on your EKS cluster

![Screenshot 2024-06-11 230707](https://github.com/yogeshsunny05/CloudNative-MonitoringApp/assets/139520226/805b5943-7463-4391-9876-0aa6f40bc8bd)

