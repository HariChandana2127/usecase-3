pipeline {
    agent any
    environment {
        BUCKET_NAME = 'chandana21_bucket'
        ARTIFACT_NAME = 'spring-petclinic-3.5.0-SNAPSHOT.jar'
        DOCKER_IMAGE_NAME = 'my-java-app-image'
    } 
    stages {
        stage('Build Artifact') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Push Artifact to Bucket') {
            steps {
                withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEY')]) {
                    sh '''
                      set -e
                      gcloud auth activate-service-account 'k8s-infra-sa@gowtham-project-476511.iam.gserviceaccount.com' --key-file=$GCP_KEY
                      gcloud config set project 'gowtham-project-476511'
                      gsutil cp target/${ARTIFACT_NAME} gs://${BUCKET_NAME}/
                    
                    '''
                } 
            } 
        }
        stage('Build Docker Image') {
            steps {
                sh "gsutil cp gs://${BUCKET_NAME}/${ARTIFACT_NAME} ."
                sh "docker build -t ${DOCKER_IMAGE_NAME} ."
            }
        }
    }
}
