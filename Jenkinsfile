pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK11"
    } 

    environment {
        SNAP_REPO = 'profile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'admin1'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'pro-maven-central'
        NEXUSIP = '172.31.37.255'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'pro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
    }

    stages {
        stage('BUILD') {
            steps {
                script {
                    sh 'mvn -s settings.xml -DskipTests install'
                }
            }
            post {
                success {
                    echo 'Now Archiving.'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn -s settings.xml test'
                }
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                script {
                    sh 'mvn checkstyle:checkstyle'
                }
            }
        }

        stage('CODE_ANALYSIS_with_SONARQUBE') {
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }

            steps {
                    withSonarQubeEnv("${SONARSERVER}") {
                        // sh """${scannerHome}/bin/sonar-scanner \
                        sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                            -Dsonar.projectKey=vprofile \
                            -Dsonar.projectName=vprofile \
                            -Dsonar.projectVersion=1.0 \
                            -Dsonar.sources=src/ \
                            -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                            -Dsonar.junit.reportsPath=target/surefire-reports/ \
                            -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                            -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                    }

                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('NexusArtifactUploader') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                        groupId: 'QA',
                        version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                        repository: "${RELEASE_REPO}",
                        credentialsId: "${NEXUS_LOGIN}",
                        artifacts: [
                            [artifactId: 'vproapp',
                             classifier: '',
                             file: 'target/vprofile-v2.war',
                             type: 'war']
                        ]
                    )
                }
            }
        }
    }
}
