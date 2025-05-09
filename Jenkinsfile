pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID = "585008057136"
        AWS_DEFAULT_REGION = "ap-south-1"
        IMAGE_REPO_NAME = "first_ecr"
        IMAGE_TAG = "latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }

    stages {
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
                        aws configure set default.region ap-south-1
                        aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 585008057136.dkr.ecr.ap-south-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/RIDDHIGANOR/jenkins-docker-image-ecr-push.git']]
                ])
            }
        }

        stage('Building image') {
            steps {
                script {
                    docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Pushing to ECR') {
            steps {
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
                    sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG}"
                }
            }
        }
    }
}
