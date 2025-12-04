pipeline {
    agent any

    environment {
        APP_NAME   = "helloworld"            // change if your jar name is different
        APP_VERSION = "0.0.1-SNAPSHOT"       // match your pom.xml version
        NEXUS_URL  = "http://localhost:8081" // or http://<vm-ip>:8081
        NEXUS_REPO = "jenkins-repo"          // the raw hosted repo you created
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Jar with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload Jar to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-admin',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PW'
                )]) {
                    sh '''
                      ls target
                      JAR_FILE=$(ls target | grep ".jar" | head -n 1)
                      echo "Uploading $JAR_FILE to Nexus..."

                      curl -v -u $NEXUS_USER:$NEXUS_PW \
                        --upload-file target/$JAR_FILE \
                        ${NEXUS_URL}/repository/${NEXUS_REPO}/com/example/${APP_NAME}/${APP_VERSION}/${APP_NAME}-${APP_VERSION}.jar
                    '''
                }
            }
        }
    }
}
