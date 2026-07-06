stage('Login to Amazon ECR') {
    steps {
        withAWS(credentials: 'aws-ecr', region: 'us-east-1') {
            sh '''
                aws ecr get-login-password --region us-east-1 | \
                docker login --username AWS --password-stdin 560696397902.dkr.ecr.us-east-1.amazonaws.com
            '''
        }
    }
}