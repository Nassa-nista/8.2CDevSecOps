pipeline {
  agent any

  environment {
    NODEJS_HOME = 'C:\\Program Files\\nodejs'
    PATH = "${env.NODEJS_HOME};${env.PATH}"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/Nassa-nista/8.2CDevSecOps.git'
      }
    }

    stage('Verify Node') {
      steps { bat 'node -v && npm -v' }
    }

    stage('Install Dependencies') {
      steps { bat 'npm ci' }
    }

    stage('Run Tests') {
      steps {
        script {
          def testStatus = bat(returnStatus: true, script: 'npm test')
          if (testStatus != 0) {
            echo "Tests failed, but continuing pipeline."
          }
          emailext(
            subject: "Run Tests - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            to: "s225381845@deakin.edu.au",
            body: """
              <h3>Stage: Run Tests</h3>
              <p><b>Status:</b> ${currentBuild.currentResult}</p>
              <p><b>Job:</b> ${env.JOB_NAME} #${env.BUILD_NUMBER}</p>
              <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
            """,
            mimeType: 'text/html'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        bat 'npm run coverage || exit 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          def auditStatus = bat(returnStatus: true, script: 'npm audit')
          if (auditStatus != 0) {
            echo "NPM Audit found vulnerabilities, but continuing pipeline."
          }
          emailext(
            subject: "NPM Audit - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            to: "s225381845@deakin.edu.au",
            body: """
              <h3>Stage: NPM Audit</h3>
              <p><b>Status:</b> ${currentBuild.currentResult}</p>
              <p><b>Job:</b> ${env.JOB_NAME} #${env.BUILD_NUMBER}</p>
              <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
            """,
            mimeType: 'text/html'
          )
        }
      }
    }
  }
}
