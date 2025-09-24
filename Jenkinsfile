pipeline {
    agent any

    environment {
        APP_NAME = "spring-petclinic-17"
        DOCKER_HUB_USER = "umatanna9"       // your Docker Hub username
        DOCKER_IMAGE = "${DOCKER_HUB_USER}/${APP_NAME}"
        HOST_PORT = "8081"                  // host port to avoid Jenkins conflict
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shankutanna/spring-petclinic-17.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Login safely with single quotes
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d --name ${APP_NAME} -p ${HOST_PORT}:8080 ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Access the app at http://<your-server-ip>:${HOST_PORT}"
        }
        failure {
            echo "❌ Build/Deploy failed. Check logs."
        }
    }
}
