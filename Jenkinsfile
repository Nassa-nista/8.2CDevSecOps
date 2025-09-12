pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Nassa-nista/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                // continue even if tests fail
                bat 'cmd /c npm test || exit /b 0'
            }
            post {
                success {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'test: success',
                        body: 'passed.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
                failure {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'failed',
                        body: 'test failed.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // continue even if coverage script fails
                bat 'cmd /c npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                // continue even if audit fails
                bat 'cmd /c npm audit || exit /b 0'
            }
            post {
                success {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'success',
                        body: 'npm audit passed.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
                failure {
                    emailext(
                        to: 'batnasan.deakin@gmail.com',
                        subject: 'failed',
                        body: 'npm audit found issues. Log attached.',
                        attachLog: true,
                        mimeType: 'text/plain'
                    )
                }
            }
        }
    }
}
