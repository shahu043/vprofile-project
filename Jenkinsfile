pipeline {
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    environment {
        SNAP_REPO = 'profile-snapshot'
        NEXUS_USR = 'admin'
        NEXUS_PASS = 'admin'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'pro-maven-central'
        NEXUS_IP = '172.31.37.255'
        NEXUS_PORT = '8081'
        NEXUS_GRP_REPO = 'pro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
    }
    stages {
        stage ('BUILD'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
        }
    }
}