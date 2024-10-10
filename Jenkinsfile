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
        stage ("build") {
            steps {
                echo "----------- Build Started -----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build Completed -----------"
            }
        }
        stage ("test") {
            steps {
                echo "----------- Unit Test Started -----------"
                sh 'mvn surefire-report:report'
                echo "----------- Unit Test Completed -----------"
            }
        }
        stage ("SonarQube analysis") {
            environment {
                scannerHome = tool 'tweet-trend-sonar-scanner'
            }
            steps {
                withSonarQubeEnv ('tweet-trend-sonar-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
