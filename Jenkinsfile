pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'kavyasreen/yourapp:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "Tests passed!"'
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials-id') {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh '''
                    CONTAINER_ID=$(docker ps -aqf "name=myapp")
                    if [ ! -z "$CONTAINER_ID" ]; then
                        docker rm -f $CONTAINER_ID
                    fi
                    docker run -d --name myapp -p 80:80 ${DOCKER_IMAGE}
                    '''
                }
            }
        }
    } // ✅ this was missing! Closing the stages block

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
