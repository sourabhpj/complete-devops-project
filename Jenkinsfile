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
                sh 'docker build -t my-website .'
            }
        }
        stage('run docker container'){
            steps {
                sh 'docker stop my-container || true'
                sh 'docker run my-container || true'
                sh 'docker run -d -p 80:80 --name my-container my-website'
            }

        }
        stage('cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }
}