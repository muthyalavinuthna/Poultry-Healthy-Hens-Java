pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = "lakshmivinuthnamutyala"
        IMAGE_NAME = "${DOCKER_HUB_USERNAME}/healthy-hens-java:${BUILD_NUMBER}"
    }

    tools {
        maven "Maven"
        jdk "JDK17"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/muthyalavinuthna/Poultry-Healthy-Hens-Java.git', branch: 'main'
            }
        }

        stage('Build using Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Image to DockerHub') {
            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'docker-credentials',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]) {

                    sh '''
                    echo $PASSWORD | docker login -u $USERNAME --password-stdin
                    docker push $IMAGE_NAME
                    '''
                }

            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                sed -i 's|IMAGE_TAG|${BUILD_NUMBER}|g' deployment.yml
                kubectl apply -f deployment.yml
                """
            }
        }
    }

    post {

        success {
            echo "Build Successful, Image pushed & deployed to Kubernetes successfully ✅"
        }

        failure {
            echo "Pipeline Failed ❌"
        }

    }
}
