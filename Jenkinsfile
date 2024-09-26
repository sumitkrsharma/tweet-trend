pipeline {
    agent {
        node {
            label 'maven'
        }
    }
<<<<<<< HEAD
    environment {
    	PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
	}
=======
environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
>>>>>>> a12386a (added Jenkinsfile to stage branch)

    stages {
        stage ("build") {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}
