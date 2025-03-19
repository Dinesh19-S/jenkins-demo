pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "Dinesh1910/docker-app:latest"  // Change this to your registry
        CONTAINER_NAME = "docker-running-app"
        REGISTRY_CREDENTIALS = "dockerhub-dinesh"  // Jenkins credentials ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-dinesh', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    git url: "https://$GIT_USER:$GIT_TOKEN@github.com/Dinesh19-S/jenkins-demo.git", branch: 'main'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-dinesh1910', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to Container Registry') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Stop & Remove Existing Container') {
            steps {
                script {
                    sh '''
                    if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
                        docker stop $CONTAINER_NAME || true
                        docker rm $CONTAINER_NAME || true
                    fi
                    '''
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                sh 'docker run -d -p 5001:5000 --name $CONTAINER_NAME $DOCKER_IMAGE'
            }
        }
    }

    post {
        success {
            echo "Build, push, and container execution successful!"
        }
        failure {
            echo "Build or container execution failed."
        }
    }
}
