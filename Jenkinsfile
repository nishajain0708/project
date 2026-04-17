pipeline {
    agent any

    environment {
        IMAGE_NAME = "devops-html-app"
        DOCKERHUB_IMAGE = "nishajain0708/devops-html-app"
        CONTAINER_NAME = "devops-html-container"
        PORT = "9090"
        AWS_DEFAULT_REGION = "ap-south-1"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/nishajain0708/project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Tag Image') {
            steps {
                bat 'docker tag %IMAGE_NAME% %DOCKERHUB_IMAGE%'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'docker login -u %USER% -p %PASS%'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                bat 'docker push %DOCKERHUB_IMAGE%'
            }
        }

        stage('Run Local Container (Optional)') {
            steps {
                bat '''
                docker rm -f %CONTAINER_NAME% || exit 0
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

        stage('Get Public URL') {
            steps {
                script {
                    def ip = bat(script: "cd terraform && terraform output -raw public_ip", returnStdout: true).trim()
                    echo "🌐 LIVE APP URL: http://${ip}"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful! CI/CD Pipeline Completed 🚀"
        }
        failure {
            echo "❌ Pipeline Failed! Check logs."
        }
    }
}