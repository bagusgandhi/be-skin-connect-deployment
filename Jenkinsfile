pipeline {
    agent any
    stages {
        stage("Checkout code") {
            steps {
                git 'https://github.com/bagusgandhi/zkinn-api.git'
            }
        }
        stage('Build image') {
            steps {
                script {
                    app = docker.build("bagusgandhi/skinconnect:${env.BUILD_ID}")
                    }
            }
        }
        
        stage('Pushing Image') {
            environment {
               registryCredential = 'dockerhublogin'
           }
            steps{
                script {
                    docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
                        app.push("${env.BUILD_ID}")
                    }
                }
            }
        } 
    
        stage('Deploy to K8s') {
            steps{
                echo "Deployment started ..."
                sh 'ls -ltr'
                sh 'pwd'
                sh "sed -i 's/skinconnect:latest/skinconnect:${env.BUILD_ID}/g' deployment-service.yaml"
                    withKubeConfig([credentialsId: 'jenkins', serverUrl: 'https://34.124.227.233']) {
                        sh './kubectl apply -f deployment-service.yaml'
                    }
                }
            }
        }    
}