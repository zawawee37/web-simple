pipeline {
    agent any
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/zawawee37/web-simple.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t my-web-cicd .'
            }
        }
        stage('Run Container') {
            steps {
                bat 'docker rm -f my-web || exit 0'
                bat 'docker run -d --name my-web -p 5000:80 my-web-cicd'
            }
        }
    }
}
