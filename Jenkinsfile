pipeline {
    agent any
    environment {
        registry = "893813366357.dkr.ecr.us-east-1.amazonaws.com/python-docker"
    }

    stages {
        stage('Clone from SCM') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/gramme01/myPythonDockerRepo']]
                )
            }
        }

        stage('Build Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }

        stage('Pushing to ECR') {
            steps {
                sh '''aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 893813366357.dkr.ecr.us-east-1.amazonaws.com
                docker push 893813366357.dkr.ecr.us-east-1.amazonaws.com/python-docker:latest'''
            }
        }

        stage('Stop previous containers') {
            steps {
                sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
                sh 'docker ps -af name=mypythonContainer -q | xargs -r docker container rm'
            }
        }

        stage('Run docker container') {
            steps{
                sh 'docker run -d -p 8096:5000 --rm --name mypythonContainer 893813366357.dkr.ecr.us-east-1.amazonaws.com/python-docker:latest'
            }
        }
    }
}