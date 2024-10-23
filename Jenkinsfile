pipeline {
    agent any
    environment {
        dockerImage = ''
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Shruthi3012/swe-645-assignment2.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image from Dockerfile
                    dockerImage = docker.build("spachava3012/hw2-survey645:${env.BUILD_ID}")
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'f0f8df0d-5d21-4d2e-8dc2-e390d70b171b') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'which kubectl'
                    
                    sh 'kubectl apply -f k8s/survey-deployment.yaml'
                    sh 'kubectl apply -f k8s/survey-service.yaml'
                 
                    sh 'kubectl rollout status deployment/your-deployment-name'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}