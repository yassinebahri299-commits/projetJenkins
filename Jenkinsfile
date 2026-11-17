pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        DOCKERHUB_USER = 'yassinebahri'
        IMAGE_NAME     = 'my-website'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/yassinebahri299-commits/projetJenkins.git',
                    branch: 'main'
            }
        }

        stage('Build') {
            steps {
                bat 'docker build -t %DOCKERHUB_USER%/%IMAGE_NAME%:latest .'
            }
        }

        stage('Smoke Test') {
            steps {
                bat 'docker rm -f smoke-test 2>nul & exit /b 0'
                bat 'docker run -d --name smoke-test -p 8082:80 %DOCKERHUB_USER%/%IMAGE_NAME%:latest'
                bat 'ping -n 6 127.0.0.1 >nul'
                bat 'curl -f -s -o NUL http://localhost:8082'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                                  usernameVariable: 'DH_USER',
                                                  passwordVariable: 'DH_PASS')]) {
                    bat 'echo %DH_PASS%| docker login -u %DH_USER% --password-stdin'
                    bat 'docker push %DOCKERHUB_USER%/%IMAGE_NAME%:latest'
                }
            }
        }
    }

    post {
        always {
            bat 'docker rm -f smoke-test 2>nul & exit /b 0'
        }
        success {
            echo 'Build OK'
        }
        failure {
            echo 'Build KO'
        }
    }
}