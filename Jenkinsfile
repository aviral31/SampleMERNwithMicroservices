pipeline {
    agent any

    environment {
        AWS_REGION = 'ca-central-1'
        ACCOUNT_ID = '975050024946'
        IMAGE_TAG = 'latest'
        DOCKER_BUILDKIT = '0'   // Disable Buildx to avoid exec format errors
        REPO_PREFIX = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/aviral"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh '''
                  echo "✅ Checked out repository."
                  echo "Workspace structure:"
                  ls -R
                '''
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh '''
                        echo "🔐 Logging in to AWS ECR..."
                        aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    sh '''
                        echo "🛠 Building frontend image..."
                        docker build -t ${REPO_PREFIX}/samplemicroservice-frontend:${IMAGE_TAG} ./frontend

                        echo "🛠 Building helloService image..."
                        docker build -t ${REPO_PREFIX}/samplemicroservice-helloservice:${IMAGE_TAG} ./backend/helloService

                        echo "🛠 Building profileService image..."
                        docker build -t ${REPO_PREFIX}/samplemicroservice-profileservice:${IMAGE_TAG} ./backend/profileService
                    '''
                }
            }
        }

        stage('Push Images to ECR') {
            steps {
                script {
                    sh '''
                        echo "🚀 Pushing frontend image..."
                        docker push ${REPO_PREFIX}/samplemicroservice-frontend:${IMAGE_TAG}

                        echo "🚀 Pushing helloService image..."
                        docker push ${REPO_PREFIX}/samplemicroservice-helloservice:${IMAGE_TAG}

                        echo "🚀 Pushing profileService image..."
                        docker push ${REPO_PREFIX}/samplemicroservice-profileservice:${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Docker images successfully built and pushed to ECR!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for errors.'
        }
    }
}
