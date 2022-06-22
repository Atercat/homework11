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
                sh 'cd /git'
                git 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
            }
        }
    }
}