pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "rahulhnb/automated-java-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Rahul-max-dot/automated-CICD-pipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
            credentialsId: 'dockerhub-credentials',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push rahulhnb/automated-java-app:9
            '''
        }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f Deployment.yaml'
                sh 'kubectl apply -f service.yaml'
                sh 'kubectl set image deployment/automated-cicd-pipeline automated-cicd-pipeline=$DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Verify') {
            steps {
                sh 'kubectl rollout status deployment/automated-cicd-pipeline'
            }
        }
    }
}
