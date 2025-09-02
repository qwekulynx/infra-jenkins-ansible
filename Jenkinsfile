pipeline {
    agent any

    parameters {
        string(name: 'INVENTORY', defaultValue: 'inventory/hosts', description: 'Path to Ansible inventory')
        string(name: 'PLAYBOOK', defaultValue: 'site.yml', description: 'Ansible playbook to run')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Target environment')
        booleanParam(name: 'DEBUG_MODE', defaultValue: false, description: 'Run with -vvv for debug output')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/qwekulynx/infra-jenkins-ansible.git'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sshagent(['ansible_ssh']) {   // ✅ Use your SSH credential ID
                    script {
                        def debugFlag = DEBUG_MODE ? "-vvv" : ""
                        sh """
                            ansible-playbook -i ${params.INVENTORY} ${params.PLAYBOOK} -e env=${params.ENVIRONMENT} ${debugFlag}
                        """
                    }
                }
            }
        }
    }
}

