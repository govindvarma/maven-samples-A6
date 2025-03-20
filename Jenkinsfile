pipeline {
  agent any
  environment {
    GOOD_COMMIT = '98ac319c0cff47b4d39a1a7b61b4e195cfa231e5'
  }
  stages {
    stage('check out') {
      steps {
        git(url: 'https://github.com/govindvarma/maven-samples-A6', branch: 'master')
      }
    }

    stage('run') {
      steps {
        script {
          def status = sh(script: 'mvn clean test', returnStatus: true)
          if (status != 0) {
            currentBuild.result = 'UNSTABLE'
          }
        }
      }
    }

    stage('bisect') {
      when {
        expression { currentBuild.result == 'UNSTABLE' }
      }
      steps {
        script {
          def BAD_COMMIT = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
          sh "git bisect start ${BAD_COMMIT} ${env.GOOD_COMMIT}"
          sh 'git bisect run mvn clean test'
        }
      }
    }

  }
  tools {
    maven 'DHT_MVN'
    jdk 'DHT_SENSE'
  }
}