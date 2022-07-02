pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
        }
    }

    environment {
        SRC_GIT_REPO = 'https://github.com/boxfuse/boxfuse-sample-java-war-hello.git'
        BUILD_GIT_REPO = 'https://github.com/Atercat/homework11.git'
        PROD_IMAGE = 'atercat/myboxfuse:hw11'
        WAR_NAME = 'hello-1.0.war'
    }

    stages {
        stage('Download source code') {
            steps {
                dir('app') {
                    git "$SRC_GIT_REPO"
                }
                dir('mygit') {
                    git "$BUILD_GIT_REPO"
                }
            }
        }

        stage('Build artifact') {
            steps {
                dir('app') {
                    sh 'mvn clean package'
                }
            }
        }
        
        stage('Create Docker image') {
            steps {
                sh 'cp mygit/run/Dockerfile ./'
                sh 'cp app/target/$WAR_NAME ./'
                sh 'docker build --build-arg ARTIFACT=$WAR_NAME -t $PROD_IMAGE .'
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
                dir('mygit') {
                    ansiblePlaybook credentialsId: 'run_node',
                        disableHostKeyChecking: true,
                        extras: '--extra-vars "app_image=$PROD_IMAGE"',
                        inventory: 'inventory',
                        limit: 'run',
                        playbook: 'deploy_playbook.yaml'
                }
            }
        }
    }
}
