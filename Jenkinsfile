pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'ajitkumargharge/app'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ajitkumargharge-dev/myapp.git'
            }
        }

        stage('Install Dependencies & Test') {
            steps {
                // Run inside Docker manually
                sh '''
                    docker run --rm -v $PWD:/app -w /app python:3.12-slim /bin/bash -c "
                        pip install --upgrade pip &&
                        pip install -r requirements.txt &&
                        pytest --junitxml=reports.xml
                    "
                '''
            }
            post {
                always {
                    junit 'reports.xml'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_HUB_REPO:$DOCKER_IMAGE_TAG ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push $DOCKER_HUB_REPO:$DOCKER_IMAGE_TAG"
                }
            }
        }
    }
}
