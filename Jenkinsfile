pipeline {
  agent any
  stages {
    stage('plop') {
      steps {
        script {
          VIP_CENTOS7 = sh(returnStdout: true, script: """
          #!/bin/bash
          echo toto
          date
          """)
          sh" echo $VIP_CENTOS7"
        }

      }
    }
  }
}