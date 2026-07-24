pipeline {
    agent any

    tools {
        nodejs 'node7'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    sh "docker build -t ${imageName} ."
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def imageName = (env.BRANCH_NAME == 'main') ? 'nodemain:v1.0' : 'nodedev:v1.0'
                    def port = (env.BRANCH_NAME == 'main') ? '3000' : '3001'

                    // Зупинка та видалення попередніх контейнерів (якщо вони існують)
                    sh "docker stop \$(docker ps -aq) || true"
                    sh "docker rm \$(docker ps -aq) || true"

                    // Запуск нового контейнера з відповідним портом
                    sh "docker run -d --expose 3000 -p ${port}:3000 ${imageName}"
                }
            }
        }
    }
}