pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK17'
    }
    environment {
        DOCKER_IMAGE = "yourusername/my-spring-app:${env.BUILD_NUMBER}"
        RENDER_API_TOKEN = rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm
        RENDER_SERVICE_ID = 'your-service-id' // e.g., srv-xxxx
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Dev1994-code/Jetkins.git', branch: 'master'
            }
        }
        stage('Build JAR') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
        stage('Deploy to Render') {
            steps {
                sh '''
                curl -X POST \
                    -H "Authorization: Bearer ${RENDER_API_TOKEN}" \
                    -H "Content-Type: application/json" \
                    -d '{"serviceId": "'${RENDER_SERVICE_ID}'", "clearCache": true}' \
                    https://api.render.com/v1/services/${RENDER_SERVICE_ID}/deploys
                '''
            }
        }
    }
    post {
        success {
            echo 'Deployed to Render successfully!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}