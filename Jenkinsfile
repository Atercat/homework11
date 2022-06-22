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
                sh 'docker build --build-arg ARTIFACT=target/hello-1.0.war -t atercat/myboxfuse https://github.com/Atercat/homework11/raw/dev/run/Dockerfile'
            }
        }
    }
}