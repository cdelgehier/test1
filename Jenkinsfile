pipeline {
  agent any
  stages {
    stage('plop') {
      steps {
        script {
          VIP_CENTOS7 = sh(returnStdout: true, script: """
          echo toto
          date
          """)
        }

        sh """
                    cd deployment/sds_centos7
                    . ${WORKSPACE}/venv/bin/activate """
      }
    }
  }
}