pipeline {
    agent any

    environment {
        AWS_REGION = "ap-southeast-2"   // use your region
        ACCOUNT_ID = "447924746913"
        IMAGE_NAME = "devops-lab"
        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${IMAGE_NAME}"

        AWS_CREDS = credentials('aws-creds')
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME .
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                export AWS_ACCESS_KEY_ID=$AWS_CREDS_USR
                export AWS_SECRET_ACCESS_KEY=$AWS_CREDS_PSW
                
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Tag image') {
            steps {
                sh '''
                docker tag $IMAGE_NAME:latest $ECR_REPO:latest
                '''
            }
        }

        stage('Push to ECR') {
            steps {
                sh '''
                docker push $ECR_REPO:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Image pushed 🚀'
        }
        failure {
            echo 'Pipeline Failed ❌'
        }
    }
}