pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "ashraff968/web-app-ci-cd"
        DOCKER_TAG = "latest"
        DOCKERHUB_CREDENTIALS = "dockerhub-creds"
        GITHUB_CREDENTIALS = "github-creds"
        K8S_DEPLOYMENT = "webapp-deployment"
        K8S_NAMESPACE = "default"
    }
    stages {

        stage('Pull Code') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Ashreaff/CICDProject',
                    credentialsId: "${GITHUB_CREDENTIALS}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS}") {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                        echo "Image pushed to Docker Hub: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Force Kubernetes to pull the latest image and do a rolling update
                    sh """
                    kubectl set image deployment/${K8S_DEPLOYMENT} webapp=${DOCKER_IMAGE}:${DOCKER_TAG} --namespace=${K8S_NAMESPACE}
                    kubectl rollout status deployment/${K8S_DEPLOYMENT} --namespace=${K8S_NAMESPACE}
                    """
                }
            }
        }

        stage('Clean old images (optional)') {
            steps {
                script {
                    // Supprimer les images Docker locales anciennes sur Jenkins
                    sh "docker images | grep ${DOCKER_IMAGE} | awk '{print \$3}' | xargs docker rmi -f || true"
                }
            }
        }
    }
}
