pipeline {
    agent any

    environment {
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '172.31.57.160:8081'
        NEXUS_REPO = 'megha'
        GROUP_ID = 'com.example'
        CREDENTIALS_ID = '1' // Use your actual Jenkins credential ID
        PROJECT_NAME = 'my-service'
        VERSION = '1.0.0' // You can also make this dynamic if needed
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Find JAR') {
            steps {
                script {
                    // Finds the first JAR in target folder
                    JAR_FILE = sh(script: "ls target/*.jar | head -n 1", returnStdout: true).trim()
                    echo "Found JAR: ${JAR_FILE}"
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: "${NEXUS_VERSION}",
                    protocol: "${NEXUS_PROTOCOL}",
                    nexusUrl: "${NEXUS_URL}",
                    groupId: "${GROUP_ID}",
                    version: "${VERSION}",
                    repository: "${NEXUS_REPO}",
                    credentialsId: "${CREDENTIALS_ID}",
                    artifacts: [
                        [
                            artifactId: "${PROJECT_NAME}",
                            classifier: '',
                            file: "${JAR_FILE}",
                            type: 'jar'
                        ]
                    ]
                )
            }
        }
    }
}
