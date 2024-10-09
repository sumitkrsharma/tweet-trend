pipeline {
    agent {
        node {
            label 'maven'
        }
    }
<<<<<<< HEAD
<<<<<<< HEAD
    environment {
    	PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
	}
=======
environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
>>>>>>> a12386a (added Jenkinsfile to stage branch)
=======
    environment {
    	PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
	}
>>>>>>> 9142f86 (updated indentation jenkinsfile)

    stages {
        stage ("build") {
            steps {
                sh 'mvn clean deploy'
            }
        }
    }
}
