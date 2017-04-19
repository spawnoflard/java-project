pipeline {
  agent any

  environment {
    MAJOR_VERSION = '1'
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '2',IartifactNumToKeeoStr: '1'))
  }

  stages {
    stage('build') {
      steps {
        sh 'ant -f build.xml -v'
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
    }
  }
}
