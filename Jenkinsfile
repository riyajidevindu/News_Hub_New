pipeline {
    agent any 

    stages { 
        stage('SCM Checkout') {
            steps {
                retry(3) {
                    git branch: 'main', url: 'https://github.com/riyajidevindu/News_Hub_New'
                }
            }
        }
        stage('Build Backend Docker Image') {
            steps {  
                dir('backend') {
                    bat 'docker build -t riyaji/news_hub_backend:%BUILD_NUMBER% .'
                }
            }
        }
        stage('Build Frontend Docker Image') {
            steps {
                dir('frontend') {
                    bat 'docker build -t riyaji/news_hub_frontend:%BUILD_NUMBER% .'
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {

                withCredentials([string(credentialsId: 'dockerhub-password', variable: 'dockerhub-pass')]) {
                script {
                        bat "docker login -u riyaji -p %dockerhub-pass%"
                    }
                }
            }
        }
        stage('Push Backend Image') {
            steps {
                bat 'docker push riyaji/news_hub_backend:%BUILD_NUMBER%'
            }
        }
        stage('Push Frontend Image') {
            steps {
                bat 'docker push riyaji/news_hub_frontend:%BUILD_NUMBER%'
            }
        }
    }
    post {
        always {
            bat 'docker logout'
        }
    }
}