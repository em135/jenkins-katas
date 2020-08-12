pipeline {
  agent any
  stages {
    stage('Say Hello') {
      parallel {
        stage('Parallel execution') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          environment {
            skipDefaultCheckout = 'true'
          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
            unstash 'code'
          }
        }

        stage('clone down') {
          steps {
            stash(name: 'code', excludes: '.git')
          }
        }

      }
    }

  }
}