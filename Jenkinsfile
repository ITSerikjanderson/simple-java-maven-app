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
                httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', httpMode: 'POST', requestBody: '''{
                "jenkinsVersion": "${jenkins.version}",
                "jenkinsUrl":     "${jenkins.rootUrl}",
                "jobName":        "${build.parent.displayName}",
                "buildNumber":     ${build.number},
                "jobUrl": "${ ( jenkins.rootUrl + build.url ) - ( build.number + '/' )}",
                "buildUrl": "${ jenkins.rootUrl + build.url }",
                "buildLog": "${ jenkins.rootUrl + build.url }consoleText",
                "buildResult": "${build.result}",
                "artifacts": ${ json( build.artifacts.collect{ a -> jenkins.rootUrl + build.url + "artifact/" + a.href } )},
                "gitUrl": "${env.GIT_URL ?: '' }",
                "gitBranch": "${env.GIT_BRANCH ?: '' }",
                "gitCommit": "${env.GIT_COMMIT ?: '' }"
                }''', responseHandle: 'NONE', url: 'https://requestbincweber.herokuapp.com/1day2kl1'

            }
        } // end post stage
	} // end stages
} // end pipleeline