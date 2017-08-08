script {
  tokens = "${env.JOB_NAME}".tokenize('/')
  env.ORG = tokens[tokens.size()-3]
  env.REPO = tokens[tokens.size()-2]
  env.BRANCH = tokens[tokens.size()-1]
}

pipeline {
  agent {
    docker {
      image 'comptel/golang-kafkaclient:1.8'
    }
  }

  options {
    skipDefaultCheckout()
    buildDiscarder(logRotator(numToKeepStr: '30'))
    disableConcurrentBuilds()
  }

  stages {
    stage('get env') {
      steps {
        script {
              sh 'env > env.txt'
              String[] envs = readFile('env.txt').split("\r?\n")

              for(String vars: envs){
                  println(vars)
              }
          }
      }
    }

    stage('clone') {
      steps {
        script {
          env.GOWORKSPACE = "${env.BUILD_ID}" + "/go/src/" + "${env.REPO}"
          env.GOPATH = "${env.WORKSPACE}" + "/" + "${env.BUILD_ID}" + "/go"
        }
        dir("${env.GOWORKSPACE}") {
          checkout scm
        }
      }
    }

    stage('build') {
      steps {
        dir("${env.GOWORKSPACE}") {
          sh 'cd $GOWORKSPACE && go build'
        }
      }
    }

    stage('test') {
      steps {
        dir("${env.GOWORKSPACE}") {
          sh 'apt-get update && apt-get install -y mongodb'
          sh 'service mongodb start'
          sh '''mongo --quiet --eval  "printjson(db.adminCommand('listDatabases'))"'''
          sh 'cd $GOWORKSPACE && go test'
        }
      }
    }

    stage('ssh') {
      steps {
        script {
          env.DEPLOYHOST = "hilla.kapsi.fi"
          sshagent (credentials: ['ssh-demo-key']) {
            sh 'ssh -o StrictHostKeyChecking=no tatutahv@$DEPLOYHOST uptime'
          }
        }
      }
    }
  }
  post {
    always {
      sh "echo done!"
    }
  }
}
