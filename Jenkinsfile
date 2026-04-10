pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-html-app"
        CONTAINER_NAME = "devops-html-container"
        PORT = "9090"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d -p $PORT:80 --name $CONTAINER_NAME $IMAGE_NAME
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                sh '''
                cd terraform
                terraform init
                '''
            }
        }

        stage('Terraform Apply') {
            steps {
                sh '''
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