pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/zawawee37/web-simple.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t my-web-cicd .'
            }
        }
        stage('Run Container') {
            steps {
                bat 'docker rm -f my-web || true'
                bat 'docker run -d --name my-web -p 8080:80 my-web-cicd'
            }
        }
    }
}
