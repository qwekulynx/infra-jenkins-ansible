pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    credentialsId: 'GitHubID',
                    url: 'https://github.com/qwekulynx/infra-jenkins-ansible.git'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh '''
                ansible-playbook -i inventory/hosts playbook.yml
                '''
            }
        }
    }
}

