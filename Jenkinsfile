pipeline {
    agent any
    
    tools {
        maven 'maven' // Specify the tool name without version
    }
    
    environment {
        AWS_ACCOUNT_ID = "661798055669"
        AWS_DEFAULT_REGION = "ap-south-1"
        IMAGE_REPO_NAME = "jenkins-pipeline-kra"
        IMAGE_TAG = "v1"
        REPOSITORY_URI = "661798055669.dkr.ecr.ap-south-1.amazonaws.com/jenkins-pipeline-kra"
        GITHUB_REPO_URL = 'https://github.com/AjayBanbare/Springboot-HelloWorld-App.git'
        GIT_CREDENTIALS_ID = 'github'
    }
   
    stages {
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh """aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}"""
                    sh """aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}"""
                    sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
            }
        }

        stage('Cleanup Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Checkout') {
            steps {
                script {
                    git(branch: 'main', credentialsId: GIT_CREDENTIALS_ID, url: GITHUB_REPO_URL)
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'mvn clean package'
                }
            }
        }
  
        // Building Docker images
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
    
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps {  
                script {
                    sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                    sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
                }
            }
        }
    }
}
