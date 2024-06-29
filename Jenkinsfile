pipeline {
    agent any 

    stages {
        stage('SCM Checkout') {
            steps {
                script {
                    def scmVars = checkout scm
                    env.GIT_BRANCH = scmVars.GIT_BRANCH
                    echo "Checked out branch: ${env.GIT_BRANCH}"
                }
            }
        }

        stage('Check Branch') {
            steps {
                script {
                    if (env.GIT_BRANCH != 'main') {
                        echo "Not on the main branch. Current branch: ${env.GIT_BRANCH}"
                        error("Stopping the pipeline as the branch is not 'main'.")
                    }
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
        stage('Deploy with Docker Compose') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-password', variable: 'dockerhub-pass')]) {
                    script {
                        bat "docker login -u riyaji -p %dockerhub-pass%"
                    }
                }
                bat 'docker-compose down'
                bat 'docker-compose pull'
                bat 'docker-compose up -d'
            }
        }
    }
    post {
        always {
            bat 'docker logout'
        }
    }
}
