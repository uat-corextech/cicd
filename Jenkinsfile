pipeline {
    agent any
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
                sudo docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} .
                sudo docker tag ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Login & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${CREDENTIALS_ID}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                    echo "$DOCKER_PASS" | sudo docker login -u "$DOCKER_USER" --password-stdin
                    sudo docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
                    sudo docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy (Optional)') {
            steps {
                sh "sudo docker rm -f webapp || true"
                sh "sudo docker run -d --name webapp -p 80:80 ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }
    }
}
