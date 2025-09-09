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
                // allows pipeline to continue even if tests fail
                bat 'npm test || exit /b 0'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // generates coverage report if available
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat '''
                        if not exist tools mkdir tools

                        REM --- Download SonarScanner if not already there ---
                        if not exist tools\\sonar-scanner\\bin\\sonar-scanner.bat (
                          echo Downloading SonarScanner...
                          powershell -Command ^
                            "$url='https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-6.1.0.4477-windows.zip';" ^
                            "$out='scanner.zip';" ^
                            "Invoke-WebRequest -Uri $url -OutFile $out -UseBasicParsing"

                          echo Unzipping...
                          powershell -Command "Expand-Archive -Path scanner.zip -DestinationPath tools -Force"

                          for /d %%D in (tools\\sonar-scanner-*-windows) do (
                            ren "%%D" "sonar-scanner"
                          )
                          del scanner.zip
                        )

                        REM --- Run scanner (uses sonar-project.properties file) ---
                        set "SCANNER=%WORKSPACE%\\tools\\sonar-scanner\\bin\\sonar-scanner.bat"
                        "%SCANNER%" -Dsonar.token=%SONAR_TOKEN%
                    '''
                }
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                // shows known vulnerabilities (CVEs)
                bat 'npm audit || exit /b 0'
            }
        }
    }
}
