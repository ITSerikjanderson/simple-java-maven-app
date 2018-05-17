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
                publisher{
                    notifyUrl: https://requestbincweber.herokuapp.com/1day2kl1
                    
                }
                    
                $class !by-name Publish HTTP POST notification  
            }
        } // end post stage
	} // end stages
} // end pipleeline