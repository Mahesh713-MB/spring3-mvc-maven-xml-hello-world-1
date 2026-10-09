
pipeline {
    agent any

    tools {
        // This should match the Maven tool name configured in Jenkins
        maven "MVN_HOME"
    }

    environment {
        // Nexus version
        NEXUS_VERSION = "nexus3"

        // Protocol used to access Nexus
        NEXUS_PROTOCOL = "http"

        // Nexus server URL
        NEXUS_URL = "54.86.237.152:8081"

        // Repository where the artifact will be uploaded
        NEXUS_REPOSITORY = "devops"

        // Jenkins credential ID for Nexus authentication
        NEXUS_CREDENTIAL_ID = "Nexus_server"
    }

    stages {
        stage("clone code") {
            steps {
                script {
                    // Clone the source code
                    git 'https://github.com/Mahesh713-MB/spring3-mvc-maven-xml-hello-world-1.git'
                }
            }
        }

        stage("mvn build") {
            steps {
                script {
                    // Build the application
                    sh 'mvn -Dmaven.test.failure.ignore=true install'
                }
            }
        }

        stage('Verify Artifact') {
            steps {
                sh '''
                    test -f target/ncodeit-hello-world-3.0.war
                    ls -lh target/ncodeit-hello-world-3.0.war
                '''

                archiveArtifacts artifacts: 'target/ncodeit-hello-world-3.0.war',
                                 fingerprint: true
            }
        }

        stage('publish to nexus') {
            steps {
                script {
                    def groupId = 'com.ncodeit'
                    def artifactId = 'ncodeit-hello-world'
                    def artifactPath = 'target/ncodeit-hello-world-3.0.war'
                    def pomPath = 'pom.xml'
                    def artifactVersion = "${BUILD_NUMBER}"

                    if (!fileExists(artifactPath)) {
                        error "WAR file not found: ${artifactPath}"
                    }

                    if (!fileExists(pomPath)) {
                        error "POM file not found: ${pomPath}"
                    }

                    echo "Publishing ${artifactPath} to Nexus"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: groupId,
                        artifactId: artifactId,
                        version: artifactVersion,
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: 'war'
                            ]
                            [
                                artifactId: artifactId,
                                classifier: '',
                                file: pomPath,
                                type: 'pom'
                            ]
                        ]
                    )

                    echo 'Nexus upload step completed.'
                }
            }
        }
    }
}
