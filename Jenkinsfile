pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'git@github.com:qwekulynx/infra-jenkins-ansible.git'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sshagent(['ansible-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ec2-user@172.31.45.121 "
                        cd /home/ec2-user/infra-jenkins-ansible &&
                        ansible-playbook -i inventory site.yml
                        "
                    '''
                }
            }
        }
    }
}

