pipeline {

```
agent {
    docker {
        image 'maven:3.9.9-eclipse-temurin-21'
        args '--network devops-network -v /var/run/docker.sock:/var/run/docker.sock'
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

    stage('Install Python & Dependencies') {
        steps {
            sh '''
            apt-get update
            apt-get install -y python3 python3-pip
            pip3 install -r requirements.txt
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
                    sh "${scannerHome}/bin/sonar-scanner"
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
            withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
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

post {
    success {
        echo "Pipeline completed successfully!"
    }
    failure {
        echo "Pipeline failed. Check logs."
    }
}
```

}
