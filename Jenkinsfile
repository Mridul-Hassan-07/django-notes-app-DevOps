```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Start') {
            steps {
                sh '''
                    docker compose up -d --build
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    sleep 200
                    curl -f http://localhost:8081
                '''
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            --username "$DOCKER_USERNAME" \
                            --password-stdin
                    '''
                }
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                    docker compose push
                '''
            }
        }
    }

    post {
        always {
            sh '''
                docker compose down
                docker logout || true
            '''
        }
    }
}
```

