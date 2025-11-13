pipeline {
    agent any
    tools {
        jdk 'jdk'
        maven 'maven'
    }
    environment {
        APPLICATION_NAME = 'spring-petclinic'
        // DOCKER_CREDS = credentials('dockerhub_creds')
        GOOGLE_APPLICATION_CREDENTIALS = credentials('chandana-artifact-push')
    }
    stages {
        stage('checkout') {
            steps{
                cleanWs()
                sh "git clone https://github.com/spring-projects/spring-petclinic.git"
            }
        }
        stage('build') {
            steps{
                dir("/var/lib/jenkins/workspace/usecase3") {
                    echo "Building ${APPLICATION_NAME}"
                    sh "mvn clean package -DskipTests=true"
                }
            }
        }
        stage('UploadtoGCS') {
            steps{
                dir("/var/lib/jenkins/workspace/usecase3"){
                    echo "Upload to my GCS Bucket"
                    sh "gsutil cp spring-petclinic-4.0.0-SNAPSHOT.jar gs://chandana21_bucket"


                }

            }
        }
        // stage('codequality') {
        //     steps {
        //         echo "scanning ${APPLICATION_NAME} code"
        //         sh """
        //         mvn clean verify sonar:sonar \
        //               -Dsonar.projectKey=${APPLICATION_NAME} \
        //               -Dsonar.host.url=http://34.122.117.31:9000 \
        //               -Dsonar.login=sqp_748eed9b9aaa19510244007b16459219cfa845e3
        //         """
        //     }
        // }
    }
}
