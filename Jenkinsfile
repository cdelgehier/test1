pipeline {
  agent none
  stages {
    stage('Setup') {
      environment {
        GITHUB_TOKEN = 'credentials(\'TokenGithub\')'
      }
      steps {
        sh '''# clone repo
                rm -rf customer-qa1
                git clone https://${GITHUB_TOKEN}@github.com/open-io/customer-qa1.git customer-qa1
                chmod 400 customer-qa1/ssh/id_rsa
                chmod 400 customer-qa1/ssh/id_rsa.pub'''
        sh '''# Virtualenv
                virtualenv venv
                . ./venv/bin/activate
                pip install \'ansible==\'${params.ANSIBLE_VERSION}
                pip install netaddr'''
      }
    }
    stage('Wipe') {
      parallel {
        stage('Soft') {
          steps {
            sh '# Just cleanup QA'
          }
        }
        stage('hard') {
          steps {
            sh '# Kickstart QA'
          }
        }
      }
    }
    stage('Build') {
      parallel {
        stage('') {
          steps {
            sh '''# build_package.py
                ./tools/build_package.py oio-sds $OIO_SDS_TAG
                ./tools/build_package.py oio-swift $OIO_SWIFT_TAG
                ./tools/build_package.py swift3 $SWIFT3_TAG
'''
          }
        }
        stage('') {
          steps {
            sh '# deploy with ansible on QA'
          }
        }
      }
    }
  }
}