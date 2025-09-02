stage('Run Ansible Playbook') {
    steps {
        sh '''
          ssh -o StrictHostKeyChecking=no ansible@172.31.45.121 \
          "cd /path/to/project && ansible-playbook -i inventory/hosts playbook.yml"
        '''
    }
}

