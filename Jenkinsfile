pipeline {
    agent any
    stages {
        stage('clonecode') {
            steps {
                git 'https://github.com/sourabhpj/complete-devops-project.git'
            }
        }
        stage('build docker image') {
            steps {
                sh 'docker build -t my-website .'
            }
        }
        stage('run docker container'){
            steps {
                sh 'docker stop my-container || true'
                sh 'docker run my-container || true'
                sh 'docker run -d -p 80:80 --name my-contaibiner my-website'
            }
        }
    }
}