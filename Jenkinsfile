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
            withCredentials([
                string(
                    credentialsId: 'DOCKERHUB_USERNAME',
                    variable: 'DOCKERHUB_USERNAME'
                ),
                string(
                    credentialsId: 'mysql-root-password',
                    variable: 'MYSQL_ROOT_PASSWORD'
                ),
                string(
                    credentialsId: 'db-password',
                    variable: 'DB_PASSWORD'
                )
            ]) {
                sh '''
                    export DB_NAME=test_db
                    export DB_USER=root
                    export DB_PORT=3306
                    export DB_HOST=db_cont
                    export MYSQL_DATABASE=test_db

                    docker compose up -d --build
                '''
            }
        }
    }

    stage('Test') {
        steps {
            withCredentials([
                string(
                    credentialsId: 'DOCKERHUB_USERNAME',
                    variable: 'DOCKERHUB_USERNAME'
                ),
                string(
                    credentialsId: 'mysql-root-password',
                    variable: 'MYSQL_ROOT_PASSWORD'
                ),
                string(
                    credentialsId: 'db-password',
                    variable: 'DB_PASSWORD'
                )
            ]) {
                sh '''
                    export DB_NAME=test_db
                    export DB_USER=root
                    export DB_PORT=3306
                    export DB_HOST=db_cont
                    export MYSQL_DATABASE=test_db

                    sleep 200

                    curl -f http://localhost:8081
                '''
            }
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
            withCredentials([
                string(
                    credentialsId: 'DOCKERHUB_USERNAME',
                    variable: 'DOCKERHUB_USERNAME'
                )
            ]) {
                sh '''
                    docker compose push
                '''
            }
        }
    }
}

post {
    always {
        sh '''
            docker compose down || true
            sudo rm -rf ./mysql-data
        '''
    }
}

}

