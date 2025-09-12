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
        success {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Run Tests: SUCCESS - ${JOB_NAME} #${BUILD_NUMBER}',
            body: 'Tests passed successfully.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
        failure {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: 'Run Tests: FAILURE - ${JOB_NAME} #${BUILD_NUMBER}',
            body: 'Tests failed. Please check the attached log.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
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
            subject: 'Security Scan Complete - ${JOB_NAME} #${BUILD_NUMBER}',
            body: 'NPM audit completed. Found ${currentBuild.result == "UNSTABLE" ? "vulnerabilities" : "no critical issues"}. Check attached log for details.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}
