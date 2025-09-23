pipeline {
    agent any

    environment {
        IMAGE_NAME = "shankutanna/petclinic"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shankutanna/spring-petclinic-17.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh '''
                    docker rm -f petclinic || true
                    docker run -d --name petclinic -p 80:8080 $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }
    }
}
