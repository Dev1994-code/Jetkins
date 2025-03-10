pipeline {
    agent any
    tools {
        maven 'Maven'
        jdk 'JDK17'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Dev1994-code/Jetkins.git', branch: 'master'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy to Render') {
            steps {
                withCredentials([string(credentialsId: 'rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm', variable: 'rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm')]) {
                    sh '''
                    curl -X POST \
                        -H "Authorization: Bearer $rnd_b0WnbqgczJB7tJPUDoxEeeuvb3Gm" \
                        -H "Content-Type: application/json" \
                        -d \'{"serviceId": "your-service-id", "clearCache": true}\' \
                        https://api.render.com/v1/services/your-service-id/deploys
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Deployed to Render!'
        }
    }
}