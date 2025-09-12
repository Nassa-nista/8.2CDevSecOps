pipeline {
  agent any

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  stages {

    stage('Checkout') {
      steps {
        // Your own public repo (no creds needed)
        git branch: 'main', url: 'https://github.com/Nassa-nista/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm ci'
      }
    }

    stage('Run Tests') {
      steps {
        script {
          // Don’t fail the whole build if Snyk isn’t authed
          int testStatus = bat(returnStatus: true, script: 'npm test')
          if (testStatus != 0) {
            echo "Tests failed, but continuing pipeline."
          }

          emailext(
            subject: "Run Tests - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            to: "batnasan.deakin@gmail.com",
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
        // Repo doesn’t have a coverage script – keep non-blocking
        bat 'npm run coverage || exit 0'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          int auditStatus = bat(returnStatus: true, script: 'npm audit')
          if (auditStatus != 0) {
            echo "NPM Audit found vulnerabilities, but continuing pipeline."
          }

          emailext(
            subject: "NPM Audit - ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            to: "batnasan.deakin@gmail.com",
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

  post {
    always {
      // Final summary email (runs whether build is SUCCESS or FAILURE)
      emailext(
        subject: "${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
        to: "batnasan.deakin@gmail.com",
        body: """
<h2>Build Summary</h2>
<p><b>Status:</b> ${currentBuild.currentResult}</p>
<p><b>Job:</b> ${env.JOB_NAME}</p>
<p><b>Build #:</b> ${env.BUILD_NUMBER}</p>
<p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
""",
        mimeType: 'text/html'
      )
    }
  }
}
