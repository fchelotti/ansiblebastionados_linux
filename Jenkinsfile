pipeline {
    agent {
        label "sul-ansible"
    }

    options {
      disableConcurrentBuilds()
      ansiColor("xterm")
    }

    parameters {
        string(defaultValue: "10.104.244.252", name: "Server")
    }

    stages {

        stage('Ansible Clone Repo') {
            steps {
                container('ansible') {
                    git credentialsId: 'bitbucket_token', url: 'https://github.com/fchelotti/ansiblebastionados_linux.git', branch: 'develop'
                }
            }
        }

        stage('Pre Build') {
            steps {
                container('ansible') {
                    sh 'for ip in ${Server}; do sed -i "/linux/a $ip" Modulos/hosts; done'
                }
            }
        }

        stage('Apply Bastionados') {
            steps {
                withCredentials([string(credentialsId: 'jenkins_admin_passwd', variable: 'ansible_passwd'),
                string(credentialsId: 'root_password', variable: 'root_passwd')
                ]){
                container('ansible') {
                    script {
                        sh 'ansible-playbook -i Modulos/hosts Modulos/bastionados-linux.yaml -e "ansible_password=${ansible_passwd}" -e "root_password=${root_passwd}" -e "countries=Peru" -e "acronym=PE" -e "domain=${domainResult}" -e "organization_unit=\'${strOganizationUnit}\'" -e "vmname=${VMname}" -v'
                    }
                }
            } 
        }
    }
}
}