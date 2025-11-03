pipeline {
    agent any

    environment {
        AWS_REGION = 'ca-central-1'   
        ACCOUNT_ID = '975050024946' 
        IMAGE_TAG = 'latest'        
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withAWS(credentials: 'aws-jenkins-credentials', region: "${AWS_REGION}") {
                    script {
                        // Fetch ECR login command and execute
                        sh '''
                            aws ecr get-login-password --region ${AWS_REGION} | \
                            docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        '''
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo "Building Docker images for all services..."

                    // Build each image
                    sh '''
                        docker build -t ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-frontend:${IMAGE_TAG} ./frontend
                        docker build -t ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-helloservice:${IMAGE_TAG} ./helloservice
                        docker build -t ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-profileservice:${IMAGE_TAG} ./profileservice
                    '''
                }
            }
        }

        stage('Push Docker Images to ECR') {
            steps {
                script {
                    echo "Pushing Docker images to ECR..."

                    sh '''
                        docker push ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-frontend:${IMAGE_TAG}
                        docker push ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-helloservice:${IMAGE_TAG}
                        docker push ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral/samplemicroservice-profileservice:${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Docker images built and pushed successfully to ECR!'
        }
        failure {
            echo '❌ Pipeline failed — please check logs.'
        }
    }
}
