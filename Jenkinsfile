pipeline {
    agent any
    
    environment {
        
        SCANNER_HOME=tool 'sonar'
    }

    stages {

        stage('Cleaning Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('GitCheckout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/roshan-etc/frontend-react.js.git'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube scanner
                    withSonarQubeEnv('SonarQube') {
                        sh 'sonar-scanner \
                            -Dsonar.projectKey=frontend \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://34.219.115.55:9000 \
                            -Dsonar.token=$SONARQUBE_TOKEN'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for the SonarQube quality gate to pass
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Quality gate failed: ${qg.status}"
                    }
                }
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
        
        stage('DockerTAG & PUSH') {
            steps {
                sh 'docker tag frontendreactjs:latest 654654245097.dkr.ecr.us-west-2.amazonaws.com/frontendreactjs:latest'
                sh 'docker push 654654245097.dkr.ecr.us-west-2.amazonaws.com/frontendreactjs:latest'
            }
        }
        

    }
}
