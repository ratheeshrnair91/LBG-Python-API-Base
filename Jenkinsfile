pipeline {
    agent any
    environment {
        GCR_CREDENTIALS_ID='Jenkins-ID'
        IMAGE_NAME='ratheesh-lbg-python-api'
        GCR_URL='eu.gcr.io/lbg-mea-17'
        PROJECT_ID='lbg-mea-17'
        CLUSTER_NAME='ratheesh-cluster'
        LOCATION='europe-west2-c'
        CREDENTIALS_ID='Kubernetes_ID'
    }
    stages {
        stage('Build and Push to GCR') {
            steps {
                script {
                   withCredentials([file(credentialsId:GCR_CREDENTIALS_ID,variable:'GOOGLE_APPLICATION_CREDENTIALS')])  {
                    sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                   } 
                    // Configure Docker to use gcloud as a credential helper
                    sh 'gcloud auth configure-docker --quiet'  

                    // Configure Docker to use gcloud as a credential helper
                    sh 'gcloud auth configure-docker --quiet'

                    // Build the Docker image
                    sh "docker build -t ${GCR_URL}/${IMAGE_NAME}:${BUILD_NUMBER} ."

                    // Push the Docker image to GCR
                    sh "docker push ${GCR_URL}/${IMAGE_NAME}:${BUILD_NUMBER}"
                }
           }
        }
        stage('Deploy to GKE'){
            steps{
                script{
                    // Deploy to GKE using Jenkins Kubernetes Engine Plugin
                    step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'kubernetes/deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                }
            }
        }
    }
}
