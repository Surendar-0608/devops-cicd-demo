pipeline {
    agent any

    environment {
        IMAGE_NAME = "surendar0608/devops-cicd-demo"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Surendar-0608/devops-cicd-demo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                pip install pytest
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                pytest
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    sonar-scanner
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t surendar0608/devops-cicd-demo .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS')]) {

                    sh '''
                    docker login -u $DOCKER_USER -p $DOCKER_PASS
                    docker push surendar0608/devops-cicd-demo
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop devops-app || true
                docker rm devops-app || true
                docker run -d -p 5000:5000 --name devops-app surendar0608/devops-cicd-demo
                '''
            }
        }

    }
}
