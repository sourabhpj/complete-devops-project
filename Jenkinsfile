pipeline {
    agent any
    stages {
        stage('clonecode') {
            steps {
                checkout scm
            }
        }
        stage('build docker image') {
            steps {
                sh 'docker build --no-cache -t my-website .'
            }
        }
        stage('run docker container'){
            steps {
                sh 'docker rm -f my-container || true'
            
                sh 'docker run -d -p 8081:80 --name my-container my-website'
            }

        }

        stage('push to docker hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds',
                    passwordVariable: 'DOCKER_PASS',
                    usernameVariable:'Docker_USER')])

                    {
                    

                        sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"

                        sh "docker tag my-website ${DOCKER_USER}/my-website:latest"

                        sh "docker push ${DOCKER_USER}/my-website:latest"
                    }
                } 
                
                sh 'curl -f http://localhost:8081 || exit 1'
            }
        }

        stage('deploy to kubernetes') {
            steps {
                script {
                dir('my-k8s-project') {

                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f ervice.yaml'
                    sh 'kubectl rollout restart deployment my-website-deploy'
                }
            }
        }
        
        

        stage('cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }

