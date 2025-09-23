pipeline {
    agent any

    environment {
        APP_NAME = "spring-petclinic-17"
        DOCKER_HUB_USER = "your-dockerhub-username"
        DOCKER_IMAGE = "${DOCKER_HUB_USER}/${APP_NAME}"
    }

    stages {
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
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker stop ${APP_NAME} || true
                    docker rm ${APP_NAME} || true
                    docker run -d --name ${APP_NAME} -p 8080:8080 ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Access the app at http://<your-server-ip>:8080"
        }
        failure {
            echo "❌ Build/Deploy failed. Check logs."
        }
    }
}
