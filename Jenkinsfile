pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
	}
	stage('Post') { 
            steps {
                httpRequest contentType: 'APPLICATION_JSON', httpMode: 'POST', responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/1day2kl1'
            }
        }
    }
}