# Flask CI/CD Pipeline using Jenkins & GitHub Actions

## Project Overview

This project demonstrates a complete Continuous Integration and Continuous Deployment (CI/CD) pipeline for a Flask application using both **Jenkins** and **GitHub Actions**.

The project automates:

- Dependency Installation
- Unit Testing using Pytest
- Build Process
- Deployment to Staging
- Production Deployment (GitHub Release Tag)
- Email Notifications (Jenkins)

---

# Repository

GitHub Repository - Forked

https://github.com/mouleeswarand/flask_Practice.git

---

# Technologies Used

- Python 3.12
- Flask
- Pytest
- MongoDB Atlas
- Git
- GitHub
- GitHub Actions
- Jenkins
- Gmail SMTP
- Ubuntu

---

# Project Structure

```
flask_Practice
│
├── .github
│   └── workflows
│       └── flask-ci.yml
│
├── app.py
├── requirements.txt
├── test_app.py
├── Jenkinsfile
└── README.md
```

---

# Assignment 1
# Jenkins CI/CD Pipeline

## Objective

Automate the build, testing and deployment of the Flask application using Jenkins.

---

## Jenkins Pipeline Stages

### Stage 1 — Build

- Create Python Virtual Environment
- Install Python dependencies
- Install required packages

Command

```bash
python3 -m venv venv
pip install -r requirements.txt
```

---

### Stage 2 — Test

Run unit tests using Pytest.

Command

```bash
pytest
```

---

### Stage 3 — Deploy

If all tests pass,

Deploy application to Staging.

(Current assignment uses a simulated deployment.)

---

### Stage 4 — Email Notification

Email notifications are sent on

- Build Success
- Build Failure

using Gmail SMTP Server.

---

# Jenkins Configuration

## Jenkins Credentials

Created the following credentials inside Jenkins.

| Credential | Purpose |
|------------|----------|
| MONGO_URI | mongodb+srv://dmouleeswaran_db_user:3Dn8tFezX09qvY6t@cluster0.vsknysx.mongodb.net/flaskdb |
| SECRET_KEY | 7b4e6d5d2e98c6d1aafc34d98c5c6b1e5c4d8f9e1a2b3c4d5e6f7a8b9c0d1e2f |
| Gmail App Password | dmouleeswaran@gmail.com |

---

## Jenkins Plugins

Installed Plugins

- Git
- Pipeline
- Email Extension Plugin
- Credentials Binding Plugin

---

# Jenkins Poll SCM - For Automation Trigger

Configured ```Poll SCM``` for ```every minutes ***** ```

Whenever code is pushed to

```
main
```

Jenkins automatically starts a new build.

---

# Jenkins Screenshots

## Jenkins Dashboard

<img width="613" height="848" alt="image" src="https://github.com/user-attachments/assets/6bd0557e-e160-4a81-a410-0aa5f38cdfb9" />


---

## Successful Pipeline

(Build → Test → Deploy)

``` sh

pipeline {

    agent {
    label 'Jenkins-Node-sc'
    }

    environment {
        MONGO_URI = credentials('mouli-cred')
        SECRET_KEY = credentials('mouli-cred2')
    }

    stages {

        stage('Build') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                    pwd
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate

                    echo "Checking MONGO_URI..."

                    if [ -z "$MONGO_URI" ]; then
                        echo "MONGO_URI is EMPTY"
                    else
                        echo "MONGO_URI exists"
                        echo "$MONGO_URI" | cut -c1-30
                    fi

                    pytest
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Staging...'
            }
        }
    }

    post {
        success {
            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The Jenkins build completed successfully.",
                to: "ryanalstonprops@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "The Jenkins build has failed. Please check the console output.",
                to: "ryanalstonprops@gmail.com"
            )
        }
    }
}

```

---

## Email Notification

<img width="573" height="728" alt="image" src="https://github.com/user-attachments/assets/08decaf9-c8f7-4f25-8858-c5e4003280eb" />


<img width="926" height="661" alt="image" src="https://github.com/user-attachments/assets/f7d8968b-78a1-4d6c-a801-7469b4d35d01" />


---

## Jenkins Console Output

<img width="1680" height="678" alt="image" src="https://github.com/user-attachments/assets/7501cffe-4ce0-49ab-8bde-598b9764faed" />


---

# Assignment 2
# GitHub Actions CI/CD Pipeline

## Objective

Automate CI/CD using GitHub Actions.

---

## Branches

The repository contains

- main
- staging

---

## Workflow File

```
.github/workflows/flask-ci.yml
```

```
name: Flask CI/CD Pipeline

on:
  push:
    branches:
      - main
      - staging
    tags:
      - 'v*'

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    env:
      MONGO_URI: ${{ secrets.MONGO_URI }}
      SECRET_KEY: ${{ secrets.SECRET_KEY }}

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run Tests
        run: |
          pytest

      - name: Build Application
        run: |
          echo "Building Flask Application..."

      - name: Deploy to Staging
        if: github.ref == 'refs/heads/staging'
        run: |
          echo "Deploying to Staging..."

      - name: Deploy to Production
        if: startsWith(github.ref, 'refs/tags/v')
        run: |
          echo "Deploying to Production..."
```

---

## GitHub Actions Workflow

The workflow performs the following tasks.

### Install Dependencies

```bash
pip install -r requirements.txt
```

---

### Run Tests

```bash
pytest
```

---

### Build

Prepare the Flask application.



---

### Deploy to Staging

Automatically runs when code is pushed to

```
staging
```

---

### Deploy to Production

Automatically runs when a Git Tag beginning with

```
v*
```

is pushed.

Example

```
v1.0
```

---

# GitHub Secrets

The following secrets are configured inside

Settings → Secrets and Variables → Actions

| Secret Name |
|-------------|
| MONGO_URI | mongodb+srv://dmouleeswaran_db_user:3Dn8tFezX09qvY6t@cluster0.vsknysx.mongodb.net/flaskdb |
| SECRET_KEY | 7b4e6d5d2e98c6d1aafc34d98c5c6b1e5c4d8f9e1a2b3c4d5e6f7a8b9c0d1e2f |

---

# GitHub Actions Screenshots

## Successful Staging Deployment

<img width="1047" height="430" alt="image" src="https://github.com/user-attachments/assets/ec819e67-c139-4936-b69c-6051af6bc6dc" />


---

## Successful Production Deployment



<img width="1018" height="377" alt="image" src="https://github.com/user-attachments/assets/100cb794-3ebd-4898-b0e0-75eb80e7c013" />


(Tag Push)
<img width="1655" height="522" alt="image" src="https://github.com/user-attachments/assets/76c7bfca-9940-4cf8-b5c7-c5428e7c74bd" />




---

## Workflow Execution

<img width="629" height="579" alt="image" src="https://github.com/user-attachments/assets/1c5bc87a-1914-4e64-84ac-9772ebb6e3b7" />


---

## GitHub Secrets

<img width="1449" height="776" alt="image" src="https://github.com/user-attachments/assets/c575ed1a-aa64-4dd2-ad61-373347b4ad9e" />


---

# Running the Project Locally

Clone repository

```bash
git clone https://github.com/mouleeswarand/flask_Practice.git
```

Move into project

```bash
cd flask_Practice
```

Create virtual environment

```bash
python -m venv venv
```

Activate

Windows

```bash
venv\Scripts\activate
```

Linux

```bash
source venv/bin/activate
```

Install dependencies

```bash
pip install -r requirements.txt
```

Run application

```bash
python app.py
```

---

# Running Tests

```bash
pytest
```



<img width="1270" height="472" alt="image" src="https://github.com/user-attachments/assets/db6b9ee5-111f-4f41-8ebc-fbe557b9dac5" />

---

# Environment Variables

Create a `.env` file.

Example

```env
MONGO_URI=mongodb+srv://dmouleeswaran_db_user:3Dn8tFezX09qvY6t@cluster0.vsknysx.mongodb.net/flaskdb
SECRET_KEY=7b4e6d5d2e98c6d1aafc34d98c5c6b1e5c4d8f9e1a2b3c4d5e6f7a8b9c0d1e2f
```

---

# CI/CD Workflow Summary

## Jenkins

```
GitHub Push
      │
      ▼
Jenkins Trigger
      │
      ▼
Build
      │
      ▼
Test
      │
      ▼
Deploy
      │
      ▼
Email Notification
```

---

## GitHub Actions

```
Push to staging
      │
      ▼
Install Dependencies
      │
      ▼
Run Tests
      │
      ▼
Build
      │
      ▼
Deploy to Staging
```

---

```
Push Tag (v1.0)
      │
      ▼
Install Dependencies
      │
      ▼
Run Tests
      │
      ▼
Build
      │
      ▼
Deploy to Production
```

---

# Deliverables

✔ Jenkinsfile

✔ GitHub Actions Workflow

✔ README Documentation

✔ Jenkins Screenshots

✔ GitHub Actions Screenshots

✔ GitHub Repository

---

# Repository Link

https://github.com/mouleeswarand/flask_Practice.git
