pipeline {
  agent any
  options { timestamps() }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm --version'
        bat 'npm ci || npm install'
      }
    }

    stage('SonarCloud Analysis') {
      environment {
        // This must match the name you added under Manage Jenkins → Tools
        SCANNER_HOME = tool 'SonarScanner'
      }
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          // Add scanner to PATH (Windows needs backslashes)
          withEnv(["PATH+SCANNER=${SCANNER_HOME}\\bin"]) {
            // sonar-project.properties in repo supplies org & projectKey
            bat 'sonar-scanner -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=%SONAR_TOKEN%'
          }
        }
      }
    }
  }
}
