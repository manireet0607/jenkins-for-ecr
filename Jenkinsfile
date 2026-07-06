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

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                """
            }
        }

        stage('Login to Amazon ECR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'aws-ecr',
                    usernameVariable: 'AWS_ACCESS_KEY_ID',
                    passwordVariable: 'AWS_SECRET_ACCESS_KEY'
                )]) {

                    sh """
                        export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                        export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}

                        aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login \
                        --username AWS \
                        --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    """
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh """
                    docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${IMAGE_URI}:${IMAGE_TAG}
                    docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${IMAGE_URI}:latest
                """
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh """
                    docker push ${IMAGE_URI}:${IMAGE_TAG}
                    docker push ${IMAGE_URI}:latest
                """
            }
        }
    }

    post {
        success {
            echo "Docker image pushed successfully to Amazon ECR."
        }

        always {
            sh """
                docker rmi ${ECR_REPOSITORY}:${IMAGE_TAG} || true
                docker rmi ${IMAGE_URI}:${IMAGE_TAG} || true
                docker rmi ${IMAGE_URI}:latest || true
            """
        }
    }
}