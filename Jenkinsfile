pipeline {
  agent any

  options {
    timestamps()
    // default checkout is OK, but we keep an explicit one for clarity
    skipDefaultCheckout(true)
  }

  triggers {
    // Optional: have Jenkins poll GitHub every 5 minutes
    pollSCM('H/5 * * * *')
  }

  stages {
    stage('Checkout') {
      steps {
        // This only works when the job is "Pipeline script from SCM" (Option B)
        checkout scm
      }
    }

    stage('Install Dependencies') {
      steps {
        bat 'npm --version'
        // Use CI install for clean & repeatable installs
        bat 'npm ci || npm install'
      }
    }

    stage('NPM Audit (Non-Blocking)') {
      steps {
        // Let the build continue even if audit finds issues
        bat 'npm audit || exit /b 0'
      }
    }

    stage('SonarCloud Analysis') {
      environment {
        // Use a stable Windows scanner; update if Sonar shows a newer one
        SCANNER_VERSION = '6.1.0.4477'
      }
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          bat '''
            echo Downloading Sonar Scanner...
            curl -L -o sonar.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-%SCANNER_VERSION%-windows.zip

            echo Unzipping...
            powershell -Command "Expand-Archive -Path sonar.zip -DestinationPath . -Force"

            echo Finding scanner folder...
            for /d %%D in (sonar-scanner-*-windows) do set "SC=%%~fD"

            echo Running scanner...
            call "!SC!\\bin\\sonar-scanner.bat"
          '''
        }
      }
    }
  }
}
