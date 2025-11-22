pipeline {
    agent any

    environment {
        // Nom de l'image Docker que l'on va construire
        DOCKER_IMAGE = "ashraff968/web-app-ci-cd:latest"
        // ID des credentials Docker Hub que tu vas créer dans Jenkins
        DOCKERHUB_CREDENTIALS = "dockerhub-creds"
    }

    stages {
        stage('Pull Code') {
            steps {
                echo "Récupération du code depuis GitHub..."
                git branch: 'main', url: 'https://github.com/Ashreaff/CICDProject'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Construction de l'image Docker..."
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "Push de l'image Docker sur Docker Hub..."
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDENTIALS}") {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Déploiement sur Kubernetes..."
                sh """
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                """
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Pipeline échoué.'
        }
    }
}
