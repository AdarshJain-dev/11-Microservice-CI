pipeline {
    agent any

    environment {
        REGISTRY = "adarshjain428"
        IMAGE_NAME = "paymentservice"
        IMAGE_TAG = "${BUILD_NUMBER}"
        CD_REPO = "https://github.com/AdarshJain-dev/11-Microservice-CD.git"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                        sh """
                        docker build -t ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} .
                        """
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                    sh """
                    docker push ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Update CD Repo Manifest (GitOps)') {
            steps {
                withCredentials([
                    gitUsernamePassword(
                        credentialsId: 'git-cred',
                        gitToolName: 'Default'
                    )
                ]) {
                    sh """
                    rm -rf 11-Microservice-CD
                    git clone ${CD_REPO}
        
                    cd 11-Microservice-CD/paymentservice
        
                    sed -i 's|image:.*|image: ${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}|' deployment-service.yaml
        
                    git config user.name "Jenkins CI"
                    git config user.email "jenkins@ci.local"
        
                    git add deployment-service.yaml
                    git commit -m "Update cartservice image to ${IMAGE_TAG}"
                    git push origin main
                    """
                }
            }
        }

    }
}
