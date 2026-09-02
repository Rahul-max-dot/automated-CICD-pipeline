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

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f Deployment.yaml
                    kubectl apply -f service.yaml

                    kubectl set image deployment/automated-cicd-pipeline \
                        automated-cicd-pipeline=$DOCKER_IMAGE:$BUILD_NUMBER
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    kubectl rollout status deployment/automated-cicd-pipeline
                '''
            }
        }
    }
}
