pipeline {
    agent any

    environment {
        IMAGE_NAME = "myimg1"
        IMAGE_TAG = "v1"
        REGISTRY = "yourdockerhubusername" // replace with your actual Docker Hub username
    }

    stages {
        stage('checkout the code from github') {
            steps {
                git url: 'https://github.com/dhanshettiaakash/health-care-project/'
                echo 'github url checkout'
            }
        }

        stage('codecompile with akshat') {
            steps {
                echo 'starting compiling'
                sh 'mvn compile'
            }
        }

        stage('codetesting with akshat') {
            steps {
                sh 'mvn test'
            }
        }

        stage('qa with akshat') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('package with akshat') {
            steps {
                sh 'mvn package'
            }
        }

        stage('build docker image') {
            steps {
                sh 'docker build -t $REGISTRY/$IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('push docker image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    sh 'docker push $REGISTRY/$IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }

        stage('deploy to kubernetes') {
            steps {
                sh '''
                kubectl delete deployment myapp-deployment --ignore-not-found=true
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
