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
                    script{
                        pom = readMavenPom file: 'pom.xml'
                        dependencies = pom.getDependencies()
                        dependencystring = ''
                        for(dependency in dependencies){
                            dependencystring += "{\"groupId\": \"$dependency.groupId\" , \"artifactId\": \"$dependency.artifactId\", \"version\": \"$dependency.version\", \"type\": \"$dependency.type\"},"
                        }
                        echo dependencystring
                    }


                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', 
                    requestBody:  "{\"Job_Name\": \"$JOB_NAME\"" +
                                //" jenkinsVersion: $jenkins.version"+
                                " \"buildResult\": \"${currentBuild.id}\""+
                                " \"displayName\": \"$currentBuild.currentResult\""+
                                " \"dependencies\": [$dependencystring]}",
                    responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/upa56fup'

                }
            
            
        } // end post stage
	} // end stages
} // end pipleeline