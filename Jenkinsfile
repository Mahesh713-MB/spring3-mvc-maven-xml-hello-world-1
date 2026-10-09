pipeline {
    agent any 
    tools {
        // Note: this should match with the tool name configured in your jenkins instance (JENKINS_URL/configureTools/)
        maven "MVN_HOME"
        
    }
	 environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "54.86.237.152:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "devops"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "Nexus_server"
    }
    stages {
        stage("clone code") {
            steps {
                script {
                    // Let's clone the source
                    git 'https://github.com/Mahesh713-MB/spring3-mvc-maven-xml-hello-world-1.git';
                }
            }
        }
        stage("mvn build") {
            steps {
                script {
                    // If you are using Windows then you should use "bat" step
                    // Since unit testing is out of the scope we skip them
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
                    ],
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
