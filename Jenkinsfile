pipeline {
    agent any

    tools {
        maven 'MVN_HOME'
    }

    environment {
        NEXUS_VERSION = 'nexus3'
        NEXUS_PROTOCOL = 'http'
        NEXUS_URL = '54.237.105.160:8081'
        NEXUS_REPOSITORY = 'devops'
        NEXUS_CREDENTIAL_ID = 'Nexus_server'
    }

    stages {
        stage('Clone Code') {
            steps {
                git url: 'https://github.com/Mahesh713-MB/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage('Publish to Nexus') {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'

                    def artifacts = findFiles(
                        glob: "target/*.${pom.packaging}"
                    )

                    if (artifacts.length == 0) {
                        error "No ${pom.packaging} artifact found under target/"
                    }

                    def artifactPath = artifacts[0].path

                    echo "Artifact: ${artifactPath}"
                    echo "Group ID: ${pom.groupId}"
                    echo "Artifact ID: ${pom.artifactId}"
                    echo "Build number: ${env.BUILD_NUMBER}"

                    nexusArtifactUploader(
                        nexusVersion: NEXUS_VERSION,
                        protocol: NEXUS_PROTOCOL,
                        nexusUrl: NEXUS_URL,
                        groupId: pom.groupId,
                        artifactId: pom.artifactId,
                        version: "${env.BUILD_NUMBER}",
                        repository: NEXUS_REPOSITORY,
                        credentialsId: NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging
                            ],
                            [
                                artifactId: pom.artifactId,
                                classifier: '',
                                file: 'pom.xml',
                                type: 'pom'
                            ]
                        ]
                    )
                }
            }
        }
    }
}
