pipeline {
    agent any

    stages {
        stage('checkout') {
            steps {
                // तुमच्या रिपोचे अचूक नाव इथे दुरुस्त केले आहे
                git branch: 'master', url: 'https://github.com/sourabhpj/complete-devops-project.git'
            }
        }

        stage('build docker image') {
            steps {
                sh 'docker build --no-cache -t my-website .'
            }
        }

        stage('push to docker hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                        sh "docker tag my-website ${DOCKER_USER}/my-website:latest"
                        sh "docker push ${DOCKER_USER}/my-website:latest"
                    }
                }
            }
        }

        stage('deploy to kubernetes') {
            steps {
                script {
                    // तुमच्या फोल्डरच्या नावानुसार (my-k8s-project)
                    dir('my-k8s-project') {
                        sh 'kubectl apply -f deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                        sh 'kubectl rollout restart deployment my-website-deploy'
                    }
                }
            }
        }

        stage('cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }
}