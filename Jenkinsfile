pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v git:/git'
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