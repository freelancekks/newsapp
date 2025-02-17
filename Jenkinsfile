pipeline {
    agent any

    environment {
        DOCKER_IMAGE_BACKEND = "newsaggegator"
        DOCKER_IMAGE_FRONTEND = "news-aggregator-frontend"
        DOCKER_REGISTRY = "freelancekks"
    }

    stages {
        stage('Clone Repository') {
            steps {
                sh 'git clone https://github.com/freelancekks/newsaggregator.git backend'
                sh 'git clone https://github.com/freelancekks/news-aggregator-frontend.git frontend'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean package'
                    sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_BACKEND:latest .'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build --prod'
                    sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_FRONTEND:latest .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_BACKEND:latest'
                    sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_FRONTEND:latest'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker-compose down'
                sh 'docker-compose pull'
                sh 'docker-compose up -d'
            }
        }
    }
}
