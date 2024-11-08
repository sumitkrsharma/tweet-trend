pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
        NEXUS_URL = "http://3.110.157.207:8081"
        NEXUS_MAVEN_REPO = "tweet-trend-maven"
        CRENDENTIAL_ID = "nexus-credentials"
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
        stage ("Upload to Nexus") {
            steps {
                echo "----------- Jar Publish Started -------------"
                script {
                    // Define artifact details
                    def artifactPath = 'jarstaging/(*)'
                    def artifactVersion = '1.0.0'
                    def artifactGroup = 'com/valaxy/demo-workshop/'
                    def artifactName = 'demo-workshop'

                    // upload artifacts to nexus
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: artifactName,
                            classifier: '',
                            file: artifactPath,
                            type: 'jar'
                        ]
                    ],
                    credentialsId: CRENDENTIAL_ID,
                    groupId: artifactGroup,
                    nexusUrl: NEXUS_URL,
                    nexusRepositoryId: NEXUS_REPO,
                    version: artifactVersion
                }    
                echo "----------- Jar Publish Complete -----------"
            }
        }
    }
}
