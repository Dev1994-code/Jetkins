pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK17'
    }
    environment {
        DOCKER_IMAGE = "dev1994/jetkins:${env.BUILD_NUMBER}"
        RENDER_API_TOKEN = rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm
        RENDER_SERVICE_ID = 'srv-cv7u460gph6c7397s1o0' // e.g., srv-xxxx
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Dev1994-code/Jetkins.git', branch: 'main'
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
                withCredentials([usernamePassword(credentialsId: '1', usernameVariable: 'dcode1994', passwordVariable: 'loveone')]) {
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
                    -H "Authorization: Bearer $rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm" \
                    -H "Content-Type: application/json" \
                    -d '{"serviceId": "srv-cv7u460gph6c7397s1o0", "clearCache": true}' \
                    https://api.render.com/v1/services/srv-cv7u460gph6c7397s1o0/deploys
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