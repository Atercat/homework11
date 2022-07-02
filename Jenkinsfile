pipeline {
    agent {
        docker {
            image 'atercat/builder'
            args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
        }
    }

    parameters {
        string(name: "SRC_GIT_REPO", defaultValue: "https://github.com/boxfuse/boxfuse-sample-java-war-hello.git", trim: true, description: "Application Git repository")
        string(name: "BUILD_GIT_REPO", defaultValue: "https://github.com/Atercat/homework11.git", trim: true, description: "Assembly files Git repository")
        string(name: "PROD_IMAGE", defaultValue: "atercat/myboxfuse:hw11", trim: true, description: "Production Docker image name")
        string(name: "WAR_NAME", defaultValue: "hello-1.0.war", trim: true, description: "WAR-artifact file name")
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
