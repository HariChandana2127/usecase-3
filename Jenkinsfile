pipeline {
    agent any

    tools {
        jdk 'JDK'          // Make sure this matches your actual tool name
        maven 'maven'      // Make sure this matches your actual tool name
    }

    environment {
        APPLICATION_NAME = 'spring-petclinic'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                dir("${env.WORKSPACE}") {
                    echo "Building ${APPLICATION_NAME}"
                    sh "mvn clean package -DskipTests=true"
                }
            }
        }

        stage('Upload to GCS') {
            steps {
                dir("${env.WORKSPACE}") {
                    echo "Uploading JAR to GCS Bucket..."
                    sh "gsutil cp target/spring-petclinic-4.0.0-SNAPSHOT.jar gs://chandana21_bucket/"
                }
            }
        }

        // Optional SonarQube stage
        // stage('Code Quality') {
        //     steps {
        //         echo "Scanning ${APPLICATION_NAME} code"
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

