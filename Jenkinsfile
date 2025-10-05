pipeline {
    agent { label 'app' }
    environment {
        DOCKERHUB_USER = 'uatcorextech'
        IMAGE_NAME = 'webapp'
        CREDENTIALS_ID = 'dockerhub'
    }
    triggers {
    githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/uat-corextech/cicd.git'
            }
        }

        stage('Build Image') {
            steps {
                sh """
                docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} .
                docker tag ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Login & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy (Optional)') {
            steps {
                sh "docker rm -f webapp || true"
                sh "docker run -d --name webapp -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }
    }
}
