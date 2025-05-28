pipeline {
    agent any

    environment {
        NEXUS_VERSION    = 'nexus3'
        NEXUS_PROTOCOL   = 'http'
        NEXUS_URL        = '172.31.57.160:8081'       // Replace with your Nexus IP and port
        NEXUS_REPO       = 'meghna'                    // Nexus Repository name
        GROUP_ID         = 'com.visualpathit'         // From pom.xml
        CREDENTIALS_ID   = '1'                         // Jenkins Credentials ID
        PROJECT_NAME     = 'vprofile'                 // From pom.xml (artifactId)
        VERSION          = "${env.BUILD_ID}"                    // Optional - can be dynamic
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Find WAR and Upload to Nexus') {
            steps {
                script {
                    // Dynamically find the WAR file
                    def warFile = sh(script: "ls target/*.war | head -n 1", returnStdout: true).trim()

                    // Echo to confirm the WAR file
                    echo "Found WAR File: ${warFile}"

                    // Upload to Nexus
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
                                file: "${warFile}",
                                type: 'war'
                            ]
                        ]
                    )
                }
            }
        }
    }
}
