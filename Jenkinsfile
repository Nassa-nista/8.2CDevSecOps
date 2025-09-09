pipeline {
  agent any

  environment {
    // Make sure Jenkins can find Node and SonarScanner
    NODEJS_HOME = 'C:\\Program Files\\nodejs'
    SCANNER_HOME = 'C:\\sonar-scanner'                  // where you unzipped the scanner
    PATH = "${env.SCANNER_HOME}\\bin;${env.NODEJS_HOME};${env.PATH}"
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
      steps { bat 'npm ci' } // same as install but deterministic
    }

    stage('Run Tests') {
      steps { bat 'npm test -- --ci || exit /b 0' }
      post {
        always { junit allowEmptyResults: true, testResults: 'reports/junit/*.xml' }
      }
    }

    stage('Generate Coverage Report') {
      steps { bat 'npm run coverage || exit /b 0' }
      post {
        always {
          archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true, fingerprint: true
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'npm audit --audit-level=high || exit /b 0' }
    }

    // >>> NEW STAGE <<<
    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          // sonar-project.properties is already in your repo root
          bat '''
            sonar-scanner ^
              -Dsonar.projectKey=Nassa-nista_8.2CDevSecOps ^
              -Dsonar.organization=nassa-nista ^
              -Dsonar.host.url=https://sonarcloud.io ^
              -Dsonar.login=%SONAR_TOKEN%
          '''
        }
      }
    }
  }
}
