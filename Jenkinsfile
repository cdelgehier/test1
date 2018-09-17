pipeline {
  agent none
  parameters {
    choice(name: 'WIPE', choices: ['soft', 'hard'], defaultValue: 'soft', description: 'Wipe OIO or hardware')
    choice(name: 'RELEASE', choices: ['17.04', '18.04', '18.10'], defaultValue: '18.04', description: 'Openio release')
    booleanParam(name: 'REPO_UNSTABLE', defaultValue: no, description: 'Install unstable repository')
    string(name: 'PKG_SDS_VERS', defaultValue: 'latest', description: 'You can specify a version like test201807171124.git0d0a649')
    string(name: 'PKG_SWIFT_VERS', defaultValue: 'latest', description: 'You can specify a version like test201805240838.gitc1045b7')
    string(name: 'PKG_SWIFT3_VERS', defaultValue: 'latest', description: 'You can specify a version like test201806271228.git9a63376')
    choice(name: 'STORAGE_POLICY', choices: ['THREECOPIES', 'ECISALC75D1'], defaultValue: 'THREECOPIES', description: 'Storage policy')
    choice(name: 'ANSIBLE_VERSION', choices: ['2.5', '2.6', '2.7'], defaultValue: '2.5', description: 'Ansible version')
  }
  stages {
    stage('Setup') {
      environment {
        GITHUB_TOKEN = credentials('TokenGithub')
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
          when { expression { params.WIPE == 'soft' } }
          steps {
            sh '# Just cleanup QA'
          }
        }
        stage('hard') {
          when { expression { params.WIPE == 'hard' } }
          steps {
            sh '# Kickstart QA'
          }
        }
      }
    }
    stage('Build') {
      parallel {
        stage('Package') {
          steps {
            sh '''# build_package.py
                ./tools/build_package.py oio-sds $OIO_SDS_TAG
                ./tools/build_package.py oio-swift $OIO_SWIFT_TAG
                ./tools/build_package.py swift3 $SWIFT3_TAG'''
          }
        }
        stage('Deploy') {
          steps {
            sh '# deploy with ansible on QA'
          }
        }
      }
    }
  }
  post {
    failure {
      slackSend channel: "@Cédric", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
    }
    success {
      slackSend channel: "@Cédric", message: "Build Success - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
    }
  }
}
