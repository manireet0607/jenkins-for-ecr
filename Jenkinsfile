pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '560696397902'
        ECR_REPOSITORY = 'manireet/reetu'
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withAWS(credentials: 'aws-ecr', region: 'us-east-1') {
                    sh '''
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 560696397902.dkr.ecr.us-east-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh '''
                    docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${IMAGE_URI}:${IMAGE_TAG}
                    docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${IMAGE_URI}:latest
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker push ${IMAGE_URI}:${IMAGE_TAG}
                    docker push ${IMAGE_URI}:latest
                '''
            }
        }
    }

    post {
        always {
            sh '''
                docker rmi ${ECR_REPOSITORY}:${IMAGE_TAG} || true
                docker rmi ${IMAGE_URI}:${IMAGE_TAG} || true
                docker rmi ${IMAGE_URI}:latest || true
            '''
        }
    }
}