

# Docker Jenkins CI/CD Pipeline Demo

This project demonstrates a CI/CD pipeline using Jenkins to build a Docker container for a Python Flask application and push it to Docker Hub.

## Project Structure

```
.
├── app.py              # Python Flask application
├── Dockerfile          # Docker build instructions
├── Jenkinsfile         # Jenkins pipeline definition
└── requirements.txt    # Python dependencies
```

## Prerequisites

- Docker installed on the Jenkins server
- Jenkins with Docker plugin installed
- GitHub and Docker Hub accounts
- Proper credentials configured in Jenkins:
  - GitHub credentials for repository access
  - Docker Hub credentials for pushing images

## Pipeline Steps

1. **Checkout**: Pulls the latest code from GitHub repository
2. **Build**: Builds the Docker image using the Dockerfile
3. **Push**: Pushes the built image to Docker Hub
4. **Deploy**: Runs the container from the pushed image

## Jenkinsfile Configuration

The pipeline is defined in the `Jenkinsfile` with these stages:

```groovy
pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/sandesh300/docker_jenkins_demo.git'
            }
        }
        
        stage('Build') {
            steps {
                sh 'docker build -t sandesh030/docker_jenkins_demo:1.0 .'
            }
        }
        
        stage('Push') {
            steps {
                sh "echo \$DOCKERHUB_CREDENTIALS_PSW | docker login -u \$DOCKERHUB_CREDENTIALS_USR --password-stdin"
                sh 'docker push sandesh030/docker_jenkins_demo:1.0'
            }
        }
        
        stage('Deploy') {
            steps {
                sh 'docker run -d -p 5000:5000 --name myapp sandesh030/docker_jenkins_demo:1.0'
            }
        }
    }
}
```

## Dockerfile

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

## Accessing the Application

After deployment, the Flask application will be available at:
`http://<your-server-ip>:5000`

## Updating the Pipeline

1. Make changes to your code
2. Commit and push to GitHub:
   ```bash
   git add .
   git commit -m "your commit message"
   git push origin main
   ```
3. Jenkins will automatically trigger the pipeline (if webhooks are configured)
4. Monitor the pipeline execution in Jenkins

