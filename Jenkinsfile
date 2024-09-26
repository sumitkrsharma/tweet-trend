pipeline {
    agent {
        node {
            label 'maven'
        }
    }
<<<<<<< HEAD
    environment {
<<<<<<< HEAD
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }
=======
    	PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
	}
=======
environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
>>>>>>> a12386a (added Jenkinsfile to stage branch)

>>>>>>> 3876734 (updated indentation jenkinsfile)
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
    }
}
