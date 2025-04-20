pipeline {
    agent any

    environment {
        NEXUS_VERSION    = 'nexus3'
        NEXUS_PROTOCOL   = 'http'
        NEXUS_URL        = '172.31.57.160:8081'       // Replace with your Nexus IP and port
        NEXUS_REPO       = 'megs'                    // Nexus Repository name
        GROUP_ID         = 'com.visualpathit'         // From pom.xml
        CREDENTIALS_ID   = '1'                         // Jenkins Credentials ID
        PROJECT_NAME     = 'vprofile'                 // From pom.xml (artifactId)
        VERSION          = "${env.BUILD_ID}"                    // Optional - can be dynamic
    }

    stages {
          stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
	   stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }
	    
 stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonarscanner1'
          }

          steps {
            withSonarQubeEnv('sq1') {
		    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vproject -Dsonar.sources=."

               // sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
               //     -Dsonar.projectName=vprofile-repo \
               //     -Dsonar.projectVersion=1.0 \
               //     -Dsonar.sources=src/ \
               //     -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
               //     -Dsonar.junit.reportsPath=target/surefire-reports/ \
               //     -Dsonar.jacoco.reportsPath=target/jacoco.exec \
               //     -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
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

