pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'aakki2503'
        IMAGE_NAME = 'HealthCarePro'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Checkout the code from GitHub') {
            steps {
                git url: 'https://github.com/dhanshettiaakash/health-care-project/'
                echo 'GitHub URL checked out'
            }
        }
        stage('Compile Code with Akshat') {
            steps {
                echo 'Starting compilation'
                sh 'mvn compile'
            }
        }
        stage('Run Tests with Akshat') {
            steps {
                sh 'mvn test'
            }
        }
        stage('QA Check with Akshat') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Package with Akshat') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dock-password', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -dt -p 8082:8082 --name c001 ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
    }
}
