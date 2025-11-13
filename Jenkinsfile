pipeline {
    agent any

    environment {
        PROJECT_ID = "leafy-rope-472907-e3"          // Replace with your GCP project ID
        REGION = "us-central1"                      // Change if needed
        GCS_BUCKET = "chandana21-bucket"            // Your GCS bucket name
        ARTIFACT_NAME = "spring-petclinic-4.0.0-SNAPSHOT.jar"
        REPO = "docker-demo"                        // Artifact Registry repository name
        IMAGE_NAME = "${REGION}-docker.pkg.dev/${PROJECT_ID}/${REPO}/spring-petclinic"
        IMAGE_TAG = "${env.BUILD_NUMBER}"            // or "latest"
    }

    stages {

        stage('Build Java Artifact') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Upload Artifact to GCS Bucket') {
            steps {
                withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEYFILE')]) {
                    sh '''
                        echo "Authenticating with GCP..."
                        gcloud auth activate-service-account --key-file="$GCP_KEYFILE"
                        gcloud config set project ${PROJECT_ID}

                        echo "Uploading artifact to GCS..."
                        gsutil cp target/${ARTIFACT_NAME} gs://${GCS_BUCKET}/
                    '''
                }
            }
        }

        stage('Download Artifact from GCS Bucket') {
            steps {
                withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEYFILE')]) {
                    sh '''
                        echo "Authenticating again..."
                        gcloud auth activate-service-account --key-file="$GCP_KEYFILE"
                        gcloud config set project ${PROJECT_ID}

                        echo "Downloading artifact from GCS..."
                        gsutil cp gs://${GCS_BUCKET}/${ARTIFACT_NAME} ${ARTIFACT_NAME}
                    '''
                }
            }
        }

        stage('Build Docker Image Using Artifact') {
            steps {
                sh '''
                    echo "Building Docker image using artifact..."
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Push Docker Image to Artifact Registry') {
            steps {
                withCredentials([file(credentialsId: 'gcp-sa-key', variable: 'GCP_KEYFILE')]) {
                    sh '''
                        echo "Authenticating Docker with Artifact Registry..."
                        gcloud auth activate-service-account --key-file="$GCP_KEYFILE"
                        gcloud config set project ${PROJECT_ID}
                        gcloud auth configure-docker ${REGION}-docker.pkg.dev --quiet

                        echo "Pushing Docker image..."
                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                        docker push ${IMAGE_NAME}:latest
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Build and push successful!"
            echo "Image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
        always {
            cleanWs()
        }
    }
}


