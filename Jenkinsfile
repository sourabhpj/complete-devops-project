pipeline {
    agent any

    stages {

        stage('Clone Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build --no-cache -t my-website .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker rm -f my-container || true'
                sh 'docker run -d -p 8081:80 --name my-container my-website'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {

                        sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag my-website $DOCKER_USER/my-website:latest
                        docker push $DOCKER_USER/my-website:latest
                        '''
                    }
                }
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl -f http://localhost:8081 || exit 1'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                dir('my-k8s-project') {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                    sh 'kubectl rollout restart deployment my-website-deploy'
                }
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }
}