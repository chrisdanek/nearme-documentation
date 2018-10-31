pipeline {
  agent any

  environment {
    TOKEN = credentials('POS_TOKEN')
    EMAIL = "darek+ci@near-me.com"
  }

  options {
    disableConcurrentBuilds()
    timeout(time: 10, unit: 'MINUTES')
    buildDiscarder(logRotator(daysToKeepStr: '365', artifactDaysToKeepStr: '30'))
  }
  stages {
    stage('Staging') {
      environment {
        MP_URL = "https://documentation.staging-oregon.near-me.com"
      }

      when {
        branch 'master'
      }

      steps {
        slackSend (channel: "#javascript-errors", color: '#00FF00', message: "STARTED: Deploying new code to staging ${MP_URL} (${env.BUILD_URL})")
        sh 'bash -l ./scripts/build.sh'
        sh 'bash -l ./scripts/deploy.sh'
        sh 'bash -l ./scripts/test-e2e.sh'
      }

      post {
        success {
          slackSend (channel: "#javascript-errors", color: '#00FF00', message: "SUCCESS: Deployed new code to staging, tests passed (${env.BUILD_URL})")
        }

        failure {
          slackSend (channel: "#javascript-errors", color: '#FF0000', message: "FAILED: Build failed. Deploy or tests failed (${env.BUILD_URL})")
        }
      }
    }
  }
}