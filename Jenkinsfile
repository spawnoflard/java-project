pipeline {
  agent none

  environment {
    MAJOR_VERSION = '1'
  }

  stages {
    stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
    }
    stage('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
    }
    stage('Running on Centos'){
      agent {
        label 'CentOS'
      }
      steps {
        sh "wget http://spawnoflard1.mylabserver.com/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 6 9"
      }
    }
  }

  post {
    agent {
      label 'apache'
    }
    always {
      archiveArtifacts artifacts: "dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar", fingerprint: true
    }
  }
}
