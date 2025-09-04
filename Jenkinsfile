stage('SonarCloud Analysis') {
  withEnv([
    'SONAR_ORG=nassa-nista-org',                  // <-- change to your SonarCloud org key
    'SONAR_PROJECT_KEY=Nassa-nista_8.2CDevSecOps' // <-- change to your SonarCloud project key
  ]) {
    withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
      bat '''
      setlocal enabledelayedexpansion

      echo Downloading Sonar Scanner...
      rem -f: fail on HTTP errors, -S: show errors, -L: follow redirects, save to sonar.zip
      curl.exe -fSL --retry 5 --retry-all-errors -o sonar.zip ^
        https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-6.1.0.4477-windows-x64.zip

      echo Unzipping...
      powershell -NoProfile -Command "Expand-Archive -Path 'sonar.zip' -DestinationPath . -Force"

      echo Finding scanner folder...
      for /D %%D in (sonar-scanner-*-windows*) do set "SC=%%~fD"
      if not defined SC (
        echo Scanner folder not found. Listing current directory:
        dir
        exit /b 1
      )
      echo Found: !SC!

      echo Running scanner...
      call "!SC!\\bin\\sonar-scanner.bat" ^
        -D"sonar.projectKey=%SONAR_PROJECT_KEY%" ^
        -D"sonar.organization=%SONAR_ORG%" ^
        -D"sonar.host.url=https://sonarcloud.io" ^
        -D"sonar.login=%SONAR_TOKEN%" ^
        -D"sonar.sources=." ^
        -D"sonar.exclusions=node_modules/**,**/*.test/**" ^
        -D"sonar.javascript.lcov.reportPaths=coverage/lcov.info"
      '''
    }
  }
}
