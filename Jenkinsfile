pipeline {
  agent any
  stages {
   stage('Notify Midway') {
  steps {
    echo 'Halfway there...'
    emailext(
      to: 'batnasan.deakin@gmail.com',
      subject: "Midway Notification",
      body: "The pipeline reached the Notify Midway stage.",
      attachLog: true
    )
  }
}
  }
}
