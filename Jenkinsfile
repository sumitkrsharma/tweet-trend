def registry = 'http://13.126.51.20:8082'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
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
        stage ("Jar Publish") {
            steps {
                script {
                    echo "----------- Jar Publish Started -----------"
                    def server = Artifactory.newServer url:registry+"/artifactory" , credentialsId:"jenkins-jfrog-token" 
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "jarstaging/(*)",
                                "target": "tweet_trend_dev/{1}",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo "----------- Jar Publish Complete -----------"
                }
            }
        }
    }
}
