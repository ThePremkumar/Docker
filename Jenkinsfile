pipeline {
    agent any

    environment {
        IMAGE_TAG = "v${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {

                echo "Building Docker image: html:${IMAGE_TAG}"

                sh """
                    docker build -t html:${IMAGE_TAG} .
                """
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the Docker container...'
                sh """
                    docker run -d \
                        --name test_container_${BUILD_NUMBER} \
                        -p 8081:80 \
                        html:${IMAGE_TAG}

                    sleep 3

                    curl -f http://localhost:8081

                    docker stop test_container_${BUILD_NUMBER}
                    docker rm test_container_${BUILD_NUMBER}
                """
            }
        }

        stage('Validation') {
            steps {
                echo 'Validating Docker image...'

                sh """
                    docker image inspect html:${IMAGE_TAG} > /dev/null

                    echo "Docker image validation successful."
                    echo "Image: html:${IMAGE_TAG}"
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }

        failure {
            echo "Pipeline failed."
        }

        always {
            sh """
                docker rm -f test_container_${BUILD_NUMBER} 2>/dev/null || true
            """
        }
    }
}
