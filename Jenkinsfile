
pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "585008057136"
        AWS_DEFAULT_REGION = "us-east-1"
        IMAGE_REPO_NAME = "first_ecr"
        IMAGE_TAG = "latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
    stage('Logging into AWS ECR') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'aws-creds',
                usernameVariable: 'AWS_ACCESS_KEY_ID',
                passwordVariable: 'AWS_SECRET_ACCESS_KEY'
            )]) {
                sh '''
                aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                aws configure set default.region us-east-1
                aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 212178105583.dkr.ecr.us-east-1.amazonaws.com
            '''
            }
        }
    }
    stage('Cloning Git') {
        steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/RIDDHIGANOR/jenkins-docker-image-ecr-push.git']]])
        }
    }
    // Building Docker images
    stage('Building image') {
        steps {
            script {
                //dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}" 
                docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps {
            script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
}
}