pipeline {
    agent any

    stages {
        stage('Run Ansible Playbook') {
            steps {
                sshagent (credentials: ['ansible_ssh']) {
                    sh '''
                        ansible-playbook -i inventory/hosts playbook.yml
                    '''
                }
            }
        }
    }
}

