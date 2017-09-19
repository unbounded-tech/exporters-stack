pipeline {
  agent {
    label "docker"
  }
  stages {
    stage("notify") {
      steps {
        slackSend(
          color: "info",
          message: "${env.JOB_NAME} started: ${env.RUN_DISPLAY_URL}"
        )
      }
    }
    stage("setup") {
      when {
        branch "master"
      }
      steps {
        sh "docker network create --driver overlay monitor || echo 'Network creation failed. It probably already exists.'"
      }
    }
    stage("deploy") {
      when {
        branch "master"
      }
      steps {
        sh "docker stack deploy -c exporters.yml exporters"
      }
    }
  }
  post {
    always {
      sh "docker system prune -f"
    }
    failure {
      slackSend(
        color: "danger",
        message: "${env.JOB_NAME} failed: ${env.RUN_DISPLAY_URL}"
      )
    }
    success {
      slackSend(
        color: "success",
        message: "${env.JOB_NAME} succeeded: ${env.RUN_DISPLAY_URL}"
      )
    }
  }
}