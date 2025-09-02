pipeline {
    agent any

    parameters {
        choice(name: 'ENVIRONMENT', choices: ['dev','staging','prod'], description: 'Deployment environment')
        string(name: 'REGION', defaultValue: 'us-east-1', description: 'AWS region')
        string(name: 'AMI', defaultValue: 'ami-0c02fb55956c7d316', description: 'AMI ID (region-specific)')
        string(name: 'INSTANCE_TYPE', defaultValue: 't3.micro', description: 'EC2 instance type')
        string(name: 'INSTANCE_COUNT', defaultValue: '1', description: 'Number of instances to create')
        string(name: 'KEY_NAME', defaultValue: 'infra_key', description: 'EC2 keypair name in AWS')
        string(name: 'KEY_FILE', defaultValue: '~/.ssh/infra_key.pem', description: 'Private key path on Ansible server')
        string(name: 'TAG_NAME', defaultValue: 'infra-instance', description: 'Name tag for EC2 instances')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run Ansible on control node') {
            steps {
                sshagent (credentials: ['ansible_ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ansible@13.218.197.75 bash -s <<'ENDSSH'
                            set -euo pipefail
                            cd ~/infra-jenkins-ansible || exit 1
                            git fetch --all
                            git reset --hard origin/main || git pull origin main
                            pip3 install --user -r requirements.txt
                            ansible-galaxy collection install amazon.aws --force
                            ansible-playbook playbooks/site.yml --extra-vars "environment=${ENVIRONMENT} region=${REGION} ami=${AMI} instance_type=${INSTANCE_TYPE} instance_count=${INSTANCE_COUNT} key_name=${KEY_NAME} key_file=${KEY_FILE} tag_name=${TAG_NAME}"
ENDSSH
                    """
                }
            }
        }
    }

    post {
        success { echo "Provisioning completed successfully." }
        failure { echo "Provisioning failed. Check logs for details." }
    }
}

