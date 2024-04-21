pipeline {
    agent any
    environment {
        AZURE_RESOURCE_GROUP  = 'demomlops'
        AZURE_AKS_CLUSTER  = 'aks-demo'

        DOCKER_IMAGE_NAME  = 'tsdb'
        BUILD_NUMBER = '0.2'
 
    }


    stages {
        stage('Checkout Code') {
            steps {
                // Checkout code from GitHub
                git 'https://github.com/danhYYR/clean-architecture-python.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                // Build Docker image from Dockerfile
                sh 'docker build . -t webapp -f docker-files/webapp-Dockerfile ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} .'
                
                // Tag Docker image with 'latest'
                sh 'docker tag ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} ${DOCKER_IMAGE_NAME}:latest'
            }
        }
        
        stage('Push to ACR') {
            steps {
                // Log in to Azure Container Registry
                sh "docker login ${ACR_SERVER} -u ${ACR_USERNAME} -p ${ACR_PASSWORD}"
                
                // Push Docker images to Azure Container Registry
                sh "docker push ${ACR_SERVER}/${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}"
                sh "docker push ${ACR_SERVER}/${DOCKER_IMAGE_NAME}:latest"
            }
        }
        
        // stage('Prepare Deployment YAML') {
        //     steps {
        //         // Copy default deployment and service YAML files and make necessary changes
        //         sh 'cp default-deployment.yaml deployment-${BUILD_NUMBER}.yaml'
        //         sh 'sed -i "s/IMAGE_TAG/${BUILD_NUMBER}/g" deployment-${BUILD_NUMBER}.yaml'
        //     }
        // }
        
        stage('Deploy to AKS') {
            steps {
                // Configure kubectl for AKS
                sh "az aks get-credentials --resource-group ${AZURE_RESOURCE_GROUP} --name ${AZURE_AKS_CLUSTER}"
                
                // Apply modified YAML files to AKS
                sh "cd k8s &&kubectl apply -f deployment.yaml"
            }
        }
    }

}