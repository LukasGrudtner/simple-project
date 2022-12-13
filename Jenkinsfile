pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    CI = true
    ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
    JFROG_CLI_BUILD_NAME = "${env.JOB_NAME}"
    JFROG_CLI_BUILD_NUMBER = "${env.BUILD_NUMBER}"
  }
  tools {
      maven '3.8.6'
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install'
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
        sh 'jf rt upload --url http://192.168.1.112:8889/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/simple-project-0.0.1-SNAPSHOT.jar simple-project/'
      }
    }

//     stage ('Run JFrog CLI') {
//         steps {
//             // sh 'jf rt mvn clean install' // build & deploy artifacts
//             sh 'jf rt upload --url http://192.168.1.112:8889/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar java-web-app/' // publish build info
//         }
//     }
  }
}