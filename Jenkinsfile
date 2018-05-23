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
                        plugins = pom.getPluginRepositories()
                        version = pom.getVersion()
                        artifactId = pom.getArtifactId()
                        dependencystring = ''
                        pluginstring = ''
                        for(dependency in dependencies){
                            dependencystring += "{\"artifactId\": \"$dependency.artifactId\", \"version\": \"$dependency.version\", \"type\": \"$dependency.type\"},"
                        }
                        for(plugin in plugins){
                            id = plugin.getId()
                            echo "$id"
                        }
                    }


                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', 
                    requestBody:  "{\"id\": \"$artifactId\"," +
                                " \"version\": \"$version\","+
                                " \"dependencies\": [$dependencystring]}",
                    responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/upa56fup'

                }
            
            
        } // end post stage
	} // end stages
} // end pipleeline