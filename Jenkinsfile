pipeline {
    agent any
    environment {
        // These IDs must match exactly what you created in Jenkins
        AWS_ID = 'aws-keys'
        SSH_ID = 'ssh-key-file'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Terraform Apply') {
            steps {
                withCredentials([aws(credentialsId: "${AWS_ID}", accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                }
            }
        }
        stage('Ansible Deploy') {
            steps {
                echo "Waiting for instances to initialize..."
                sleep 45
                withCredentials([file(credentialsId: "${SSH_ID}", variable: 'SSH_KEY_PATH')]) {
                    sh "ansible-playbook -i inventory.ini setup.yml --private-key ${SSH_KEY_PATH} --ssh-common-args='-o StrictHostKeyChecking=no'"
                }
            }
        }
    }
    post {
        success {
            echo "Deployment successful! Access the frontend IP on port 80."
        }
    }
}
