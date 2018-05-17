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
                httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', 
                requestBody:  "{Job_Name: $JOB_NAME}",
                 responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/1day2kl1'

            }
        } // end post stage
	} // end stages
} // end pipleeline