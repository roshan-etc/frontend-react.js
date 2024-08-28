pipeline {
    agent any

    stages {
        stage('GitCheckout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/roshan-etc/frontend-react.js.git'
            }
        }

        stage('DockerBuild ') {
            steps {
                sh 'docker build -t frontendreactjs .'
            }
        }
        stage('DockerLogin') {
            steps {
                sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 654654245097.dkr.ecr.us-west-2.amazonaws.com'
            }
        }
        
        stage('DockerTAG&PUSH') {
            steps {
                sh 'docker tag frontendreactjs:latest 654654245097.dkr.ecr.us-west-2.amazonaws.com/frontendreactjs:latest'
                sh 'docker push 654654245097.dkr.ecr.us-west-2.amazonaws.com/frontendreactjs:latest'
            }
        }
        

    }
}
