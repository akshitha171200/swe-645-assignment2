pipeline {
    agent any
    environment {
        dockerImage = ''
        KUBECONFIG = "${WORKSPACE}/swe645-assignment.yaml" // Updated kubeconfig file path
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
                    // Read token from Jenkins credentials
                    withCredentials([string(credentialsId: 'ed50af5f-aa48-4f53-b4d7-615d00b21278', variable: 'TOKEN')]) {
                        // Update token in kubeconfig file
                        sh """
                            sed -i 's|token:.*|token: ${TOKEN}|g' swe645-assignment.yaml
                        """
                    }
                    
                    // Use the modified kubeconfig file
                    withEnv(["KUBECONFIG=${WORKSPACE}/swe645-assignment.yaml"]) {
                        sh 'kubectl apply -f survey-deployment.yaml'
                        sh 'kubectl apply -f survey-service.yaml'
                        sh 'kubectl rollout restart deployment/assignment2-survey-deployment'
                        sh 'kubectl rollout status deployment/assignment2-survey-deployment'
                    }
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
