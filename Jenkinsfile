pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Starting Build'
        sh 'mvn clean install -Dlicense.skip=true'
        echo 'Build Complete'
      }
    }
  }
  tools {
    maven 'maven'
  }
}