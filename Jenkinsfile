pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo "Checkout repo..."
                git branch: 'main', url: 'https://github.com/Nassa-nista/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo "Installing dependencies ..."
                bat 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                echo "Running tests..."
                script {
                    def result = bat(script: 'npm test', returnStatus: true)
                    if (result != 0) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
            post {
                always {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'Test Stage Complete - Build ${BUILD_NUMBER}',
                        body: 'Test stage has completed. Status: ${BUILD_STATUS}. Please see attached logs.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
            }
        }
        stage('Generate Coverage Report') {
            steps {
                echo "Generating coverage report ..."
                bat 'npm run coverage || exit /b 0'
            }
        }
        stage('NPM Audit (Security Scan)') {
            steps {
                echo "Auditing..."
                script {
                    def result = bat(script: 'npm audit', returnStatus: true)
                    if (result != 0) {
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
            post {
                always {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'Security Scan Complete - Build ${BUILD_NUMBER}',
                        body: 'Security scan completed. Found vulnerabilities as expected in nodejs-goof project. Check logs for details.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
            }
        }
    }
}
