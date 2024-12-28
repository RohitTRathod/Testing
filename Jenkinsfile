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

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubCredentials', 
                                                  usernameVariable: 'DOCKER_USERNAME', 
                                                  passwordVariable: 'DOCKER_PASSWORD')]) {
                    // Log in to Docker Hub
                    bat "docker login -u %DOCKER_USERNAME% -p %DOCKER_PASSWORD%"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                bat 'docker build -t rohittrathod/testing .'
            }
        }

        stage('Run Docker Container') {
            steps {
                // Run the Docker container
                bat 'docker run -d -p 8089:8080 rohittrathod/testing'
            }
        }

        stage('Deploy to Minikube') {
    steps {
        withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG_FILE')]) {
            sh '''
            export KUBECONFIG=${KUBECONFIG_FILE}
            echo "Current context:"
            kubectl config current-context
            echo "Kubeconfig file contents:"
            cat ${KUBECONFIG_FILE}
            echo "Applying deployment..."
            kubectl apply -f deployment.yaml
            echo "Applying service..."
            kubectl apply -f service.yaml
            '''
        }
    }
}
    }
}