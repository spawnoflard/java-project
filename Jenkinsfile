pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
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
      post {
        success {
          archiveArtifacts artifacts: "dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar", fingerprint: true
        }
      }
    }
    stage('deploy') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        sh "mkdir /var/www/html/rectangles/all/${env.MAJOR_VERSION}_${env.BUILD_NUMBER}"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.MAJOR_VERSION}_${env.BUILD_NUMBER}"
      }
    }
    stage('Running on Centos') {
      agent {
        label 'CentOS'
      }
      steps {
        sh "wget http://spawnoflard1.mylabserver.com/rectangles/all/${env.MAJOR_VERSION}_${env.BUILD_NUMBER}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 6 9"
      }
    }
    stage("Test on Debian") {
      agent {
        docker 'openjdk:8u121-jre'
      }
      steps {
        sh "wget http://spawnoflard1.mylabserver.com/rectangles/all/${env.MAJOR_VERSION}_${env.BUILD_NUMBER}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 60 20"
      }
    }
    stage('Promote Branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo 'Stash Any Local Changes'
        sh 'git stash'
        echo 'Checking Out Development Branch'
        sh 'git checkout development'
        sh 'git pull'
        echo 'Checking Out the Master Branch'
        sh 'git checkout master'
        echo 'Merging development to master'
        sh 'git merge development --no-ff'
        echo 'Pushing to Origin Master'
        sh 'git push origin master'
        echo 'Tagging Release'
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/${env.MAJOR_VERSION}_${env.BUILD_NUMBER}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/"
      }
      post {
        failure {
          emailext(
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
            body: """<p>''${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QuOT;</p>""",
            to: 'steven.briggs69@gmail.com'
          )
        }
      }
    }
  }
  post {
    success {
      emailext(
        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted to Master!",
        body: """<p>''${env.JOB_NAME} [${env.BUILD_NUMBER}]' Success! Development Promoted to Master":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QuOT;</p>""",
        to: 'steven.briggs69@gmail.com'
      )
    }
  }
}
success {
  emailext(
    subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted to Master!",
    body: """<p>''${env.JOB_NAME} [${env.BUILD_NUMBER}]' Success! Development Promoted to Master":</p>
    <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QuOT;</p>""",
    to: 'steven.briggs69@gmail.com'
  )
}
