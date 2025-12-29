pipeline {
    agent any

    environment {
        REGISTRY = "adarshjain428"
        IMAGE_NAME = "adservice"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CD_REPO = "https://github.com/AdarshJain-dev/11-Microservice-CD.git"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh """
                        docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .
                        """
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred') {
                    sh """
                    docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Update CD Repo Manifest (GitOps)') {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'git-cred', gitToolName: 'Default')]) {
                    sh """
                    git clone ${CD_REPO}
                    cd OnlineBuitique-CD/cartservice

                    sed -i 's|image: .*|image: ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}|' deployment.yaml

                    git config user.name "Jenkins CI"
                    git config user.email "jenkins@ci.local"
                    git add deployment.yaml
                    git commit -m "Update cartservice image to ${IMAGE_TAG}"
                    git push origin main
                    """
                }
            }
        }
    }
}
