pipeline {
    agent any

    stages {
        stage('Run Ansible Playbook') {
            steps {
                sshagent (credentials: ['AnsibleSSHKey']) {
                    sh '''
                        ansible-playbook -i inventory/hosts playbook.yml
                    '''
                }
            }
        }
    }
}

