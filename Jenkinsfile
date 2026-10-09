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
stage('publish to nexus') {
    steps {
        script {
            // Properly declare the variable with 'def'
            def mavenPom = readMavenPom file: 'pom.xml'
            
            // Now you can safely access properties without triggering global field errors
            echo "Project Name: ${mavenPom.artifactId}"
            echo "Project Version: ${mavenPom.version}"
            echo "Packaging: ${mavenPom.packaging}"
            
            // Add your Nexus publishing steps here (e.g., using Maven deploy)
            sh 'mvn deploy -DskipTests'
        }
    }
}
