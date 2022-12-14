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
    IMAGE = readMavenPom().getArtifactId()
    VERSION = readMavenPom().getVersion()
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

    stage ('Upload to Artifactory') {
        steps {
            sh 'jf rt upload --url http://192.168.1.112:8889/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/${IMAGE}-${VERSION}.jar simple-project/'
        }
    }

    stage('Sonar analysis') {
      steps {
        withSonarQubeEnv(installationName: 'sonarqube') {
          sh 'mvn clean sonar:sonar'
        }
      }
    }
  }
}