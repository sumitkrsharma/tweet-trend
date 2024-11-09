pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
        NEXUS_URL = "http://13.233.141.210:8081/"
        NEXUS_MAVEN_REPO = "tweet-trend-maven"
        NEXUS_CRENDENTIAL_ID = "nexus-credentials"
    }
    stages {
        stage ("Maven Build") {
            steps {
                echo "----------- Build Started -----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed -----------"
            }
        }
        stage ("Maven Test") {
            steps {
                echo "----------- Unit Test Started -----------"
                sh 'mvn surefire-report:report'
                echo "----------- Unit Test Completed -----------"
            }
        }
        stage ("SonarQube Analysis") {
            environment {
                scannerHome = tool 'tweet-trend-sonar-scanner'
            }
            steps {
                withSonarQubeEnv ('tweet-trend-sonar-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                sleep 30
            }
        }
        stage ("Quality Gate") {
            steps {
                script {
                    timeout (time: 10, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg:status}"
                        }
                    }
                }
            }
        }
        stage ("Verify Build Artifact") {
            steps {
                echo "Verifying that the artifact exists"
                sh "ls -l jarstaging/com/valaxy/demo-workshop/2.1.2/"
            }
        }
        stage ("Test Nexus Connectivity") {
            steps {
                sh 'curl -I http://13.233.141.210:8081/'
            }
        }
        stage ("Upload to Nexus") {
            steps {
                echo "----------- Jar Publish Started -----------"
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '13.233.141.210:8081',
                    groupId: 'com.valaxy',
                    version: '2.1.2',
                    repository: 'tweet-trend-maven',
                    credentialsId: 'nexus-credentials',
                    artifacts: [
                        [
                            artifactId: 'demo-workshop',
                            classifier: '',
                            file: 'jarstaging/com/valaxy/demo-workshop/2.1.2/demo-workshop-2.1.2.jar',
                            type: 'jar'
                        ]
                    ]
                )
            }
        }
    }
}
