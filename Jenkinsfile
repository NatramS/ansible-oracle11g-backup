
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout your code repository
                git 'https://your-repo-url.git'
            }
        }
        
        stage('Terraform Apply') {
            steps {
                // Run terraform apply to provision infrastructure
                sh 'terraform init'
                sh 'terraform apply -auto-approve'
            }
        }
        
        stage('Configure Web Servers') {
            steps {
                // Run ansible playbook to configure web servers
                sh 'ansible-playbook -i inventory.ini configure_web_servers.yml'
            }
        }
    }
    
    post {
        success {
            // Cleanup steps if needed
            echo 'Deployment successful!'
        }
        failure {
            // Cleanup steps if needed
            echo 'Deployment failed!'
        }
    }
}
