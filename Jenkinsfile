pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "aakki2503"                             // ✅ Your Docker Hub username
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "v1"
        FULL_IMAGE = "${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout the code from GitHub') {
            steps {
                echo '📦 Checking out code from GitHub...'
                git url: 'https://github.com/dhanshettiaakash/health-care-project/'
            }
        }

        stage('Compile the code') {
            steps {
                echo '🔧 Compiling the application...'
                sh 'mvn compile'
            }
        }

        stage('Run unit tests') {
            steps {
                echo '🧪 Running unit tests...'
                sh 'mvn test'
            }
        }

        stage('Run QA checks') {
            steps {
                echo '🔍 Running code quality checks...'
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Package the app') {
            steps {
                echo '📦 Packaging the application...'
                sh 'mvn package'
            }
        }

        stage('Build Docker image') {
            steps {
                echo "🐳 Building Docker image: ${FULL_IMAGE}"
                sh "docker build -t ${FULL_IMAGE} ."
            }
        }

        stage('Push Docker image to Docker Hub') {
            steps {
                echo "📤 Pushing Docker image to Docker Hub..."
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    sh "docker push ${FULL_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "🚀 Deploying to Kubernetes..."
                sh '''
                sed -i "s|image:.*|image: ${FULL_IMAGE}|" k8s/deployment.yaml
                kubectl delete deployment myapp-deployment --ignore-not-found=true
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check the error above."
        }
    }
}
