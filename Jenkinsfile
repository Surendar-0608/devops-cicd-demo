pipeline {
    agent {
        docker {
            image 'python:3.9'
            args '-u root --network devops-network'
        }
    }

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
                pip install -r requirements.txt
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
        script {
            def scannerHome = tool 'sonar-scanner'
            withSonarQubeEnv('SonarQube') {
                sh '''
                apt-get update
                apt-get install -y openjdk-17-jre
                export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
                export PATH=$JAVA_HOME/bin:$PATH
                ${scannerHome}/bin/sonar-scanner
                '''
            }
        }
    }
}
        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t $IMAGE_NAME .
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
                    docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop devops-app || true
                docker rm devops-app || true
                docker run -d -p 5000:5000 --name devops-app $IMAGE_NAME
                '''
            }
        }

    }
}
