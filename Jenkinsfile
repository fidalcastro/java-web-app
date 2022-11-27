pipeline {
  agent { label 'linux' }
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    CI = true
    ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
  }
  stages {
    stage('Build') {
      steps {
        sh './mvnw clean install'
      }
    }
    stage('Upload to Artifactory') {
      agent {
        docker {
          image 'releases-docker.jfrog.io/jfrog/jfrog-cli-v2:2.2.0' 
          reuseNode true
        }
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'artifactory-jenkins', passwordVariable: 'password', usernameVariable: 'user')]) {
          sh 'jfrog rt upload --url http://192.168.1.230:8082/artifactory/ --user ${user} --password ${password} target/demo-0.0.1-SNAPSHOT.jar java-web-app/'
        }
      }
    }
  }
}