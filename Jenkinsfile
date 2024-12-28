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
                bat 'docker run -d -p 8088:8080 rohittrathod/testing'
            }
        }
    }

    post {
        always {
            // Clean up: Stop and remove the container if it exists
            bat '''FOR /F "tokens=*" %i IN ('docker ps -q --filter "ancestor=name/devops-project"') DO docker stop %i'''
            bat '''FOR /F "tokens=*" %i IN ('docker ps -aq --filter "ancestor=name/devops-project"') DO docker rm %i'''
        }
    }
}
