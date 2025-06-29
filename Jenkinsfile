// Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git clone 'https://github.com/zawawee37/web-simple.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                bash 'docker build -t my-web-cicd .'
            }
        }
        stage('Run Container') {
            steps {
                bash 'docker rm -f my-web || true'
                bash 'docker run -d --name my-web -p 8080:80 my-web-cicd'
            }
        }
    }
}
