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
        script {
          // run tests and capture exit code
          def code = bat(returnStatus: true, script: 'npm test')
          if (code != 0) {
            currentBuild.result = 'FAILURE'
          }
        }
      }
      post {
        success {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: "Tests passed - Build ${env.BUILD_NUMBER}",
            body: 'All tests passed.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
        failure {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: "Tests FAILED - Build ${env.BUILD_NUMBER}",
            body: 'Tests failed. See attached log.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        // optional; don’t fail the build if missing
        bat 'npm run coverage || exit /b 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          def code = bat(returnStatus: true, script: 'npm audit')
          // mark unstable if vulnerabilities are expected/allowed
          if (code != 0) {
            currentBuild.result = 'UNSTABLE'
          }
        }
      }
      post {
        success {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: "npm audit clean - Build ${env.BUILD_NUMBER}",
            body: 'npm audit reported no issues.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
        unsuccessful {
          // fires for FAILURE or UNSTABLE
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: "npm audit found issues - Build ${env.BUILD_NUMBER}",
            body: 'npm audit reported vulnerabilities. See attached log.',
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}
