pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
        }
    }
    stages {
        stage('Download source code') {
            steps {
                git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
            }
        }

        stage('Build artifact') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Create Docker image') {
            steps {
                sh 'wget -P target https://github.com/Atercat/homework11/raw/dev/run/Dockerfile'
                sh 'docker build --build-arg ARTIFACT=hello-1.0.war -t atercat/myboxfuse:hw11 target'
            }
        }

        stage('Push Docker image to repository') {
            environment {
                REPO_CREDS = credentials('dockerhub')
            }
            steps {
                sh 'docker login -u $REPO_CREDS_USR -p $REPO_CREDS_PSW'
                sh 'docker push atercat/myboxfuse:hw11'
            }
        }
    }
}