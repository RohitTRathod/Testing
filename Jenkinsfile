pipeline {
    agent any  // Use any available agent

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout the code from the Git repository
                git branch: 'main', url: 'https://github.com/RohitTRathod/Testing.git'
                // List the contents of the workspace for debugging
                bat 'dir'  // Use 'ls' instead of 'bat' for Linux agents
            }
        }

        stage('Install Dependencies') {
            steps {
                // Change to the directory containing package.json if needed
                dir('app') {  // Adjust 'app' to your actual directory
                    bat 'npm install'  // Use 'sh' instead of 'bat' for Linux agents
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        // Log in to Docker Hub, build and push the Docker image
                        bat "docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%"
                        bat 'docker build -t rohittrathod/pretest .'
                        bat 'docker tag rohittrathod/pretest rohittrathod/pretest:latest'
                        bat 'docker push rohittrathod/pretest:latest'
                    }
                }
            }
        }

        stage('Deploy to Minikube') {
            steps {
                withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG')]) {
                    script {
                        // Apply the Kubernetes deployment and service YAML files
                        bat 'kubectl apply -f deployment.yaml --validate=false'  // Adjust the path as necessary
                        bat 'kubectl apply -f service.yaml --validate=false'     // Adjust the path as necessary
                    }
                }
            }
        }
    }
}
