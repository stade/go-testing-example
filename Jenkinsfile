pipeline {
  agent {
    docker {
      image 'golang:1.9'
    }

  }
  stages {
    stage('build') {
      steps {
        sh 'go build'
      }
    }
    stage('clone') {
      steps {
        sh 'cd $BUILD_ID/go/src/ && git clone https://github.com/stade/go-testing-example.git'
      }
    }
    stage('export go path') {
      steps {
        sh 'cd $BUILD_ID/go && export GOPATH=$(pwd) && echo $GOPATH'
      }
    }
    stage('build') {
      steps {
        sh 'cd $BUILD_ID/go/src/go-testing-example && go build'
      }
    }
    stage('test') {
      steps {
        sh 'cd $BUILD_ID/go/src/go-testing-example && go test'
      }
    }
  }
}
