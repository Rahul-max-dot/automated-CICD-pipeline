```groovy
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

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'rahulhnb',
                        passwordVariable: 'dckr_pat_rbRK6y0-3-j3l4QvgsiAIxwGN-s'
                    )
                ]) {
                    sh '''
                        echo "dckr_pat_rbRK6y0-3-j3l4QvgsiAIxwGN-s" | docker login \
                            -u "$rahulhnb" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                        -t $DOCKER_IMAGE:$BUILD_NUMBER .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                    docker push $DOCKER_IMAGE:$BUILD_NUMBER
                '''
            }
        }

        stage('Minikube Setup') {
            steps {
                sh '''
                    minikube status || minikube start --driver=docker

                    kubectl config use-context minikube

                    kubectl get nodes
                '''
            }
        }

        stage('Load Image into Minikube') {
            steps {
                sh '''
                    minikube image load $DOCKER_IMAGE:$BUILD_NUMBER
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f Deployment.yaml
                    kubectl apply -f service.yaml

                    kubectl set image deployment/automated-cicd-pipeline \
                        automated-cicd-pipeline=$DOCKER_IMAGE:$BUILD_NUMBER

                    kubectl rollout status deployment/automated-cicd-pipeline
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    echo "Kubernetes Nodes:"
                    kubectl get nodes

                    echo "Pods:"
                    kubectl get pods

                    echo "Services:"
                    kubectl get services

                    echo "Deployment:"
                    kubectl get deployment automated-cicd-pipeline
                '''
            }
        }
    }
}
```
