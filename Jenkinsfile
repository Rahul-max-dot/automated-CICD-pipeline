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
            usernameVariable: 'rahulhnb',
            passwordVariable: 'dckr_pat_Wy_9PRNMfmZs5UHyTSQhIDkQtb8'
        )]) {
            sh '''
                echo "dckr_pat_Wy_9PRNMfmZs5UHyTSQhIDkQtb8" | docker login -u "rahulhnb" --password-stdin
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
