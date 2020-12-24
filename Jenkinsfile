pipeline {
  agent {
    node {
      label 'sdf'
    }

  }
  stages {
    stage('sdf') {
      agent {
        node {
          label 'stage'
        }

      }
      steps {
        sh 'echo \'s\''
      }
    }

  }
}