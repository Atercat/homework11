pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
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
                fileOperations([fileCopyOperation(excludes: '', flattenFiles: false, includes: 'run/Dockerfile', renameFiles: false, sourceCaptureExpression: '', targetLocation: '', targetNameExpression: '')])
                sh 'docker build --build-arg ARTIFACT=target/hello.war -t atercat/myboxfuse .'
            }
        }
    }
}