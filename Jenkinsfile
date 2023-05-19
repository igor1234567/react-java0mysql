pipeline {
    agent { 
        label 'nexus_ubuntu' 
    }

    stages {
        stage('checkout code') {
            steps {
                // Clone the repository containing the Docker Compose file
                checkout([$class: 'GitSCM', branches: [[name: '*/dev']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-ssh', url: 'git@github.com:igor1234567/react-java0mysql.git']]])
            }
        }

        stage('Build and deploy') {
            steps {
                // Build and deploy the Docker Compose file
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }

        stage('Run tests') {
            steps {

                // Run custom test script
                sh 'bash run_tests.sh'

                // Perform API testing using cURL commands
                sh 'curl -X GET http://localhost:8080/api/test'
                sh 'curl -X POST -d "data=example" http://localhost:8080/api/test'
            }
        }

        stage('Build and Push To Nexus Image') {
            steps {
                    withCredentials([usernamePassword(credentialsId: 'nexus_user', passwordVariable: 'nexus_pass', usernameVariable: 'nexus_user')]) {
                        sh '''docker tag igorripin/react-java0mysql:${BUILD_ID} ec2-35-158-255-27.eu-central-1.compute.amazonaws.com:8083/react-java0mysql:${BUILD_ID}'''
                        sh '''docker login ec2-35-158-255-27.eu-central-1.compute.amazonaws.com:8083 -u $nexus_user -p $nexus_pass'''
                        sh '''docker push ec2-35-158-255-27.eu-central-1.compute.amazonaws.com:8083/react-java0mysql:${BUILD_ID}'''
                 }
            }
        }

        stage('Cleanup') {
            steps {
                // Stop and remove the containers
                sh 'docker-compose down'
            }
        }
    }
}
