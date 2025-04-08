pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "aakki2503"                       // ✅ Your actual Docker Hub username
        IMAGE_NAME = "myapp"
        IMAGE_TAG = "v1"
        FULL_IMAGE = "${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Checkout the code from GitHub') {
            steps {
                git url: 'https://github.com/dhanshettiaakash/health-care-project/'
                echo '✅ Code checked out from GitHub'
            }
        }

        stage('Compile the code') {
            steps {
                echo '🔧 Starting compilation...'
                sh 'mvn compile'
            }
        }

        stage('Run unit tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Run QA checks') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Package the app') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Build Docker image') {
            steps {
                sh "docker build -t ${FULL_IMAGE} ."
            }
        }

        stage('Push Docker image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    sh "docker push ${FULL_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                sed -i "s|image:.*|image: ${FULL_IMAGE}|" k8s/deployment.yaml
                kubectl delete deployment myapp-deployment --ignore-not-found=true
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
