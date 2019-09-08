pipeline {
    agent any
    environment {
        GITHUB_URL = 'https://github.com/MichaelVL/grafanalib-container'
        REGISTRY = 'michaelvl/grafanalib'
        REGISTRY_CREDENTIALS_ID = 'DockerHub'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: "$GITHUB_URL"]]])
                script {
                    env.GIT_COMMIT = sh (
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true).trim()
                }
            }
        }
        stage('Prepare IDs') {
            steps {
                script {
                    NOW = sh (
                        script: 'date +%Y%m%d-%H%M',
                        returnStdout: true).trim()
                    env.NOW = NOW
                    env.FULLNAME = "$REGISTRY:git-$NOW-$GIT_COMMIT"
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build "$FULLNAME"
                }
            }
        }
        stage('Push Image') {
            steps{
                script {
                    docker.withRegistry( '', REGISTRY_CREDENTIALS_ID ) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
