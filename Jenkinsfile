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
                git branch: 'main', url: 'https://github.com/nishajain0708/project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $IMAGE_NAME $DOCKERHUB_IMAGE'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([string(credentialsId: 'docker-pass', variable: 'PASS')]) {
                    sh 'docker login -u YOUR_DOCKERHUB_USERNAME -p $PASS'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $DOCKERHUB_IMAGE'
            }
        }

        stage('Run Local Container (Optional)') {
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

        stage('Get Public URL') {
            steps {
                script {
                    def ip = sh(script: "cd terraform && terraform output -raw public_ip", returnStdout: true).trim()
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