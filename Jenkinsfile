// Jenkinsfile (Pipeline)
pipeline {
    agent any
    environment {
        dockerhub_credentials = credentials('Docker')
    }
    stages {
        stage('Docker Image Build') {
            steps {
                echo 'Building docker image...'
                sh 'docker build --tag lmcint301/cw2-server:1.0 .'
                echo 'Docker built successfully!'
            }
        }
        stage('Test Docker Image') {
            steps {
                echo 'Testing Docker Image...'
                sh '''
                    docker image inspect lmcint301/cw2-server:1.0
                    docker run --name test-container -p 8081:8080 -d lmcint301/cw2-server:1.0
                    docker ps
                    docker stop test-container
                    docker rm test-container
                '''
            }
        }
        stage('DockerHub Login') {
            steps {
                sh 'echo $dockerhub_credentials_PSW | docker login -u $dockerhub_credentials_USR --password-stdin'
            }
        }

        stage('Dockerhub Image Push') {
            steps {
                sh 'docker push lmcint301/cw2-server:1.0'
            }
        }
        stage('Deploy') {
           steps {
                sshagent(['my-ssh-key']) {
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@35.173.129.221 'kubectl set image deployment/coursework cw2-server=docker.io/lmcint301/cw2-server:1.0'
                }
            }
        }
    }
}
