pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    stages {
        stage('Build') {
            steps {
                git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
                sh 'mvn clean package'
            }
        }
    }
}