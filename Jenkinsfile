script {
  tokens = "${env.JOB_NAME}".tokenize('/')
  env.ORG = tokens[tokens.size()-3]
  env.REPO = tokens[tokens.size()-2]
  env.BRANCH = tokens[tokens.size()-1]
  // env.GOWORKSPACE = "${env.WORKSPACE}" + "/" + "${env.BUILD_ID}" + "/go/src/" + "${env.REPO}"
  // env.GOPATH = "${env.WORKSPACE}" + "/" + "${env.BUILD_ID}" + "/go"
}

pipeline {
  agent {
    docker {
      image 'comptel/golang-kafkaclient:1.8'
    }
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
          sh 'go build'
        }
      }
    }

    stage('test') {
      steps {
        dir("${env.GOWORKSPACE}") {
          sh 'go test'
        }
      }
    }
  }
}
