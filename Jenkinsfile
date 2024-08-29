pipeline {
    agent any
    
    environment {
        // Define the SonarQube Scanner tool
        SCANNER_HOME = tool 'sonar'
        // Reference the Node.js installation directly by name
        NODEJS_HOME = tool 'nodejs'
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

        stage('Install') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube scanner with the updated server URL and token
                    withSonarQubeEnv('sonar') {
                        sh '''
                        ${SCANNER_HOME}/bin/sonar-scanner \
                          -Dsonar.projectKey=frontend \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=http://35.90.114.54:9000 \
                          -Dsonar.token=sqp_07fa25d48e7c11d4ad67e85b2e0e3602e632bd90
                        '''
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

        stage('DockerBuild') {
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


    post {
        always {
            cleanWs() // Clean workspace after pipeline runs
        }
    }
}
