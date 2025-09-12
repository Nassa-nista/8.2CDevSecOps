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
        bat 'npm --version'
        bat 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        // keep the "continue on failure" behavior if you want
        bat 'cmd /c npm test || exit /b 0'
      }
    }
  }
}
