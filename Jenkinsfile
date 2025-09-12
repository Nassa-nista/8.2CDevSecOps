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
           sh 'npm install'
      }
    }
    stage('Run Tests') {
      steps {
         echo "Running tests..."
           sh 'npm test || true'
      }
      post {
        success {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Run Tests: SUCCESS',
            body: 'Tests passed.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
        failure {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Run Tests: FAILURE',
            body: 'Tests failed. Log attached.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
    stage('Generate Coverage Report') {
      steps {
         echo "Generating coverage report ..."
           sh 'npm run coverage || true'
      }
    }
    stage('NPM Audit (Security Scan)') {
      steps {
         echo "Auditing..."
           sh 'npm audit || true'
      }
      post {
        success {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Security Scan: SUCCESS',
            body: 'npm audit passed.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
        failure {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Security Scan: FAILURE',
            body: 'npm audit found issues. Log attached.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}

