pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        stash(name: 'code', excludes: '.git')
      }
    }

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
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash 'code'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

        stage('Test app') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          options {
            skipDefaultCheckout(true)
          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('build docker') {

      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
      }
    }

    stage('push docker') {
      when { branch "master" }
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        sh 'echo "On master branch"'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
      }
    }
    
    stage('component test') {
     when {
      not {
         branch "dev/**"
      }
    }
    when { changeRequest() }
     steps {
       sh 'echo "this is a change request"'
       sh 'ci/component-test.sh'
     }
    }

  }
  environment {
    docker_username = 'em135'
  }
  post {
    always {
      deleteDir()
    }

  }
}
