pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
        }
    }

    environment {
        SRC_GIT_REPO = 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        DOCKER_FILE = 'https://github.com/Atercat/homework11/raw/dev/run/Dockerfile'
        PLAYBOOK = 'https://github.com/Atercat/homework11/raw/dev/run/deploy_playbook.yaml'
        PROD_IMAGE = 'atercat/myboxfuse:hw11'
        WAR_NAME = 'hello-1.0.war'
    }

    stages {
        stage('Download source code') {
            steps {
                git "$SRC_GIT_REPO"
            }
        }

        stage('Build artifact') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Create Docker image') {
            steps {
                sh 'wget -P target $DOCKER_FILE'
                sh 'docker build --build-arg ARTIFACT=$WAR_NAME -t $PROD_IMAGE target'
            }
        }

        stage('Push Docker image to repository') {
            environment {
                REPO_CREDS = credentials('dockerhub')
            }
            steps {
                sh 'docker login -u $REPO_CREDS_USR -p $REPO_CREDS_PSW'
                sh 'docker push $PROD_IMAGE'
            }
        }

        stage('Deploy image') {
            steps {
                sh 'wget -P target $PLAYBOOK'
                ansiblePlaybook become: true,
                    credentialsId: 'run_node',
                    disableHostKeyChecking: true,
                    extras: '--extra-vars "app_image=$PROD_IMAGE"',
                    inventory: 'inventory',
                    limit: 'run',
                    playbook: 'deploy_playbook.yaml'
            }
        }
    }
}