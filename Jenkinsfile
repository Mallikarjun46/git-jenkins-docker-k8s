pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "mallikarjun46/pipeline-app"
        DOCKER_TAG = "latest"
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Cloning from GitHub...'
                git branch: 'main',
                   url: 'https://github.com/Mallikarjun46/git-jenkins-docker-k8s.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
    steps {
        echo 'Pushing to Docker Hub...'
        withCredentials([usernamePassword(
            credentialsId: 'Dockerhub-credential',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push mallikarjun46/pipeline-app:latest
            '''
        }
    }
}

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes...'
                sh "kubectl apply -f deployment.yaml"
                sh "kubectl apply -f service.yaml"
                sh "kubectl rollout status deployment/pipeline-app"
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
