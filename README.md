# Capstone Project: E-Commerce Application CI/CD Pipeline
# Project Overview
This project focuses on developing and automating the CI/CD pipeline for a full-stack e-commerce platform. The platform consists of:

1) E-Commerce API: Backend service to manage product listings, user accounts, and order processing.
2) E-Commerce Frontend: A React-based web application for user interaction.
The goal is to automate testing, building, and deployment using GitHub Actions, Docker, and a cloud platform.

# Task 1: Project Setup
Steps:
  
### 1) Create a GitHub Repository:
- Name the repository: ECOMMERCE-FULLSTACKAPP.
### 2) Directory Structure:
- Inside the repository, create two directories:

   - backend: For the  API.
   - frontend: For the for web application

## Task 2: Initialize GitHub Actions
### Steps:
### Initialize Git Repository:
1) Inside your project directory, initialize git:
    ```
        git init
    ```
 #### 2) Add Initial Structure:
 + Add the project directories and an initial README.
### 3) Create GitHub Actions Directory:
  + Inside the repository, create a .github/workflows directory:

     ```
           mkdir -p .github/workflows   
     ```     
## Task 3: Backend API Setup
### Steps:
 1) Initialize Node.js Project:
     + Navigate to the backend directory and set up a basic Node.js project:
      ```
       cd backend
       npm init -y
       ```
 2) Install Dependencies:
    + Install Express and other necessary dependencies:
    ```
    npm install express.
    ```
3) Basic backend  Setup:
   + Create an index.js file to define basic routes (e.g., product listing, order creation).
  
4) Unit Testing:
   + Install a testing framework like Jest:
        ```
           npm install --save-dev jest
        ````   
    + Add unit tests for the API functionality.


## Task 4: Frontend Web Application Setup
### Steps:
1) Create React Application:
   + In the frontend directory, create a React app:
      ```
       npx create-react-app webapp
       ````

2) Basic Features:
     + Implement basic functionality for product listing, user login, and order placement.

 3) API Integration:
       + Ensure the React app interacts with the backend API.     

## Task 5: Continuous Integration Workflow
Steps:
1) Create GitHub Actions Workflow:
    + Inside .github/workflows, create a new YAML file for the CI workflow.
    + The workflow should:
        <ul style="list-style-type: square;">
          <li>Install dependencies.</li>
          <li>Run tests for both backend and frontend.</li>
          <li>Build the application.</li>
        </ul>

```
name: CI Workflow

on: [push, pull_request]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install
      - run: npm test
        working-directory: ./backend

  webapp-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install
      - run: npm test
        working-directory: ./frontend
```        
## Task 6: Docker Integration
Steps:
1) Create Dockerfile for Backend:
In the backend directory, create a Dockerfile:
      ```
      FROM node:14
      WORKDIR /app
      COPY . .
      RUN npm install
      CMD ["node", "index.js"]
      ```

2) Create Dockerfile for Frontend:
In the webapp directory, create a Dockerfile:
      ```
      FROM node:14
      WORKDIR /app
      COPY . .
      RUN npm install
      RUN npm run build
      CMD ["npm", "start"]
      ```     
3) Modify GitHub Actions:

      + Update the GitHub Actions workflow to build Docker images.
          ```
          jobs:      
            build-api:
              runs-on: ubuntu-latest
              steps:
                - uses: actions/checkout@v2
                - name: Build Docker Image
                  run: docker build -t api-image ./api

            build-webapp:
              runs-on: ubuntu-latest
              steps:
                - uses: actions/checkout@v2
                - name: Build Docker Image
                  run: docker build -t webapp-image ./webapp
          ```
## Task 7: Deploy to Cloud
Steps:
1) Choose a Cloud Platform:
      + Select AWS, Azure, or GCP for deployment.
2) Docker Image Deployment:
       + Modify your GitHub Actions to push Docker images to your cloud provider’s container registry.
3) Secure Access:
       + Use GitHub Secrets to store cloud credentials and access keys.
           jobs:          
           deploy-backend:
               runs-on: ubuntu-latest
               steps:
                 - uses: actions/checkout@v2
                 - name: Log in to Cloud
                   run: echo "${{ secrets.CLOUD_KEY }}" | docker login -u ${{ secrets.USERNAME }} --password-stdin
                 - name: Push Docker Image
                   run: docker push your-cloud-repo/backend-image

             deploy-frontend:
               runs-on: ubuntu-latest
               steps:
                 - uses: actions/checkout@v2
                 - name: Log in to Cloud
                   run: echo "${{ secrets.CLOUD_KEY }}" | docker login -u ${{ secrets.USERNAME }} --password-stdin
                 - name: Push Docker Image
                   run: docker push your-cloud-repo/frontend-image
           ```        

## Task 8: Deploy The Ecr images to your aws Ec2 instance
Steps:
1) Log into Ec2 instance:
2) SSh into EC2 instance
3) install the aws cli in the Ec2 instance
  - create a file eg:aws
  - Use any of the editor to open the file eg:vi aws.sh 
  - Paste the shell script inside the file
  
      ```
           #!/bin/bash

    # Install dependencies - unzip and curl
    sudo apt-get install unzip curl -y

    # Download the AWS CLI v2 bundle
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

    # Unzip the installer
    unzip awscliv2.zip

    # Run the install program
    sudo ./aws/install

    # Verify the installation
    aws --version
    ```
  + Install cli for docker images
      ```
      #!/bin/bash

    # Install Docker and add user to the Docker group
    set -e  # Exit script immediately if a command exits with a non-zero status

    # Add Docker's official GPG key
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    # Set up Docker repository
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    # Install required packages
    sudo apt-get update
    sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common

    # Update package lists after adding Docker repository
    sudo apt-get update

    # Install Docker
    sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    # Start and enable Docker service
    sudo systemctl start docker
    sudo systemctl enable docker

    # Add the current user to the Docker group
    sudo usermod -aG docker $USER
    ```

## Task 8: Continuous Deployment
Steps:
1) Automate Deployment:
   + Configure workflows to automatically deploy to the cloud whenever changes are pushed to the main branch.

## Task 9: Project Documentation
Steps:
1) README.md:
    + Document the project setup, instructions for running locally, and details on the CI/CD workflow.
2) Instructions:
   + Include deployment instructions for both local and cloud environments.
### Conclusion
   + This project provides a comprehensive workflow to set up an e-commerce platform using Node.js and React, automate testing, building, and deployment using GitHub Actions and Docker, and deploy to Ec2 instance.












