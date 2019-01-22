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
    stage('Testing') {
      parallel {
        stage('SonarQube Test') {
          steps {
            sh 'mvn sonar:sonar -Dsonar.host.url=http://52.77.216.8:8081 -Dlicense.skip=true'
          }
        }
        stage('Print Tester Credentials') {
          steps {
            echo "The tester is ${TESTER}"
          }
        }
        stage('Print build number') {
          steps {
            echo "This is build number ${BUILD_ID}"
          }
        }
      }
    }
    stage('JFrog Push') {
      steps {
        echo 'Starting JFrog Message'
        script {
          def server = Artifactory.server "artifactory"
          def buildInfo = Artifactory.newBuildInfo()
          def rtMaven = Artifactory.newMavenBuild()

          rtMaven.tool = 'maven'
          rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'

          buildInfo = rtMaven.run pom: 'pom.xml', goals: "clean install -Dlicense.skip=true"
          buildInfo.env.capture = true
          buildInfo.name = 'jpetstore-6'
          server.publishBuildInfo buildInfo
        }

        echo 'JFrog Push Finished'
      }
    }
    stage('Deploy prompt') {
      steps {
        input 'Deploy to production?'
      }
    }
    stage('Deployment') {
      steps {
        echo 'Deployment Completed'
      }
    }
  }
  tools {
    maven 'maven'
  }
  environment {
    TESTER = 'JIA'
  }
}