pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-html-app"
        CONTAINER_NAME = "devops-html-container"
        PORT = "9090"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/nishajain0708/devopsexp'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Run Container') {
            steps {
                bat '''
                docker rm -f %CONTAINER_NAME% 2>nul || exit 0
                docker run -d -p %PORT%:80 --name %CONTAINER_NAME% %IMAGE_NAME%
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                bat '''
                cd terraform
                terraform init
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                bat '''
                cd terraform
                terraform apply -auto-approve
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}