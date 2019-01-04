pipeline {
  agent any
  stages {
    stage('plop') {
      steps {
            script {
              VIP_CENTOS7 = sh(returnStdout: true, script: ". ${WORKSPACE}/venv/bin/activate; ansible localhost -m os_port_facts  2>/dev/null | sed -e 's/.*=> //' | jq '.ansible_facts.openstack_ports[] | select(.name == "vip_1-vip").fixed_ips[].ip_address'")
            }
            sh """
            cd deployment/sds_centos7
            . ${WORKSPACE}/venv/bin/activate """
      }
    }
  }
}
