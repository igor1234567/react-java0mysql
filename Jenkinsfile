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

                // Perform API testing using cURL commands
                sh 'curl -X GET http://localhost:3000'
            }
        }

        stage('Build and Push To Nexus Image') {
            steps {
                    script {

                        // Install dependencies and build the React application
                        sh '''cp /home/vagrant/jenkins_slave/workspace/module-6/frontend/package.json /home/vagrant/jenkins_slave/workspace/module-6/package.json'''
                         sh '''cp /home/vagrant/jenkins_slave/workspace/module-6/frontend/public/index.html /home/vagrant/jenkins_slave/workspace/module-6/public/index.html'''
                        sh '''npm install'''
                        sh '''pwd'''
                        sh '''npm run build'''
                        // Build the Docker image
                        sh 'docker build . -t igorripin/react-java0mysql:${BUILD_ID}'
                }
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