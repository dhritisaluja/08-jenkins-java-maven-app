pipeline {
  agent any // agent defines where this script should be executed (relevant on Jenkins clusters)
  stages {
    stage("build") {
      steps {
        echo 'building the application...'
      }
    }
    stage("test") {
      steps {
        echo 'testing the application...'
      }
    }
    stage("deploy") {
      steps {
        echo 'deploying the application...'
      }
    }
  }
}
