pipeline {
    agent any
    environment {
        REMOTE_USER = 'devops'
        REMOTE_HOST = '192.168.1.67'
        DOCKER_IMAGE = 'asimd10/node-app-devops:1.4'
        DOCKER_CREDENTIALS_ID = 'dockerhub_token'
    }
    stages {
        stage('Clone Repository') {
            steps {
                sshagent(['homelab_ssh_key']) {
                    sh """
                    ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                    rm -rf /home/devops/docker_training/git_image/*
                    git clone --single-branch --branch main 'https://github.com/asim10/docker-node-app.git' '/home/devops/docker_training/git_image/docker-node-app'
                    << EOF"""
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                sshagent(['homelab_ssh_key']) {
                    sh """
                    ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                    cd /home/devops/docker_training/git_image/docker-node-app
                    docker build -t ${DOCKER_IMAGE} .
                    <<< EOF"""
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sshagent(['homelab_ssh_key']) {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                        ssh ${REMOTE_USER}@${REMOTE_HOST} << EOF
                        echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin
                        docker push ${DOCKER_IMAGE}
                        << EOF"""
                    }
                }
            }
        }
        
    }
}
