pipeline {
  agent any
  options { timestamps() }

  parameters {
    choice(name: 'ACTION', choices: ['provision','configure','destroy'], description: 'What do you want to run?')
    choice(name: 'ENV', choices: ['dev','prod'], description: 'Environment tag')
    string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS Region')
    string(name: 'VPC_CIDR', defaultValue: '10.42.0.0/16', description: 'VPC CIDR')
    string(name: 'PUBLIC_CIDR', defaultValue: '10.42.1.0/24', description: 'Public subnet CIDR')
    string(name: 'INSTANCE_TYPE', defaultValue: 't3.micro', description: 'EC2 size')
    string(name: 'AMI_ID', defaultValue: 'ami-xxxxxxxx', description: 'AMI ID')
    string(name: 'KEY_NAME', defaultValue: 'jenkins-demo', description: 'Existing AWS key pair name')
    booleanParam(name: 'DRY_RUN', defaultValue: false, description: 'Ansible --check mode')
  }

  environment {
    ANSIBLE_STDOUT_CALLBACK = 'yaml'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Tooling Check') {
      steps {
        sh '''
          set -e
          python3 -V
          ansible --version
          aws --version
        '''
      }
    }

    stage('Install Ansible Collections') {
      steps {
        sh 'ansible-galaxy collection install -r requirements.yml --force'
      }
    }

    stage('Run Ansible') {
      steps {
        withCredentials([
          [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-main'],
          sshUserPrivateKey(credentialsId: 'ssh-key-jenkins', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')
        ]) {
          sh '''#!/usr/bin/env bash
            set -euo pipefail

            export AWS_DEFAULT_REGION="${AWS_REGION}"
            export ANSIBLE_HOST_KEY_CHECKING=false

            EXTRA_VARS="env=${ENV} \
              vpc_cidr=${VPC_CIDR} \
              public_subnet_cidr=${PUBLIC_CIDR} \
              instance_type=${INSTANCE_TYPE} \
              ami_id=${AMI_ID} \
              key_name=${KEY_NAME}"

            case "${ACTION}" in
              provision) PLAY="playbooks/provision.yml" ;;
              configure) PLAY="playbooks/configure.yml" ;;
              destroy)   PLAY="playbooks/destroy.yml" ;;
              *) echo "Unknown ACTION"; exit 2 ;;
            esac

            # Optional: write SSH config for Ansible (if needed)
            # export ANSIBLE_PRIVATE_KEY_FILE="${SSH_KEY}"

            CMD="ansible-playbook ${PLAY} -e \\"${EXTRA_VARS}\\""
            if [ "${DRY_RUN}" = "true" ]; then
              CMD="${CMD} --check"
            fi
            echo ">>> ${CMD}"
            eval "${CMD}"
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/ansible.log', allowEmptyArchive: true
    }
  }
}

