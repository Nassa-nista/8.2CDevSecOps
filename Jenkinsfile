pipeline {
  agent any

  options { skipDefaultCheckout(true) } // optional: prevents the automatic checkout

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
            currentBuild.result = 'UNSTABLE' // don’t fail the build
          }
        }
      }
      post {
        always {
          emailext(
            to: 'batnasan.deakin@gmail.com',
            subject: "Test Stage Complete - Build ${env.BUILD_NUMBER}",
            body: """Test stage completed.
Status: ${currentBuild.currentResult}
Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}
URL: ${env.BUILD_URL}
(See attached log.)""",
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
            subject: "Security Scan Complete - Build ${env.BUILD_NUMBER}",
            body: """Security scan finished for ${env.JOB_NAME} #${env.BUILD_NUMBER}.
Status: ${currentBuild.currentResult}
URL: ${env.BUILD_URL}
(See attached log.)""",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}
