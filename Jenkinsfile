#!groovy

pipeline {
  agent any

  stages {
    stage('FetchCode') {
      steps {
        dir('cdis-manifest') {
          checkout scm
        }
        dir('cloud-automation') {
          git(
            url: 'https://github.com/uc-cdis/cloud-automation.git',
            branch:  'master'
          )
        }
      }
    }
    stage('k8sDeploy') {
      steps {
        withEnv(['GEN3_NOPROXY=true', "GEN3_HOME=$env.WORKSAPCE/cloud-automation"]) {
          echo "GEN3_HOME is $env.GEN3_HOME"
          echo "GIT_BRANCH is $env.GIT_BRANCH"
          echo "GIT_COMMIT is $env.GIT_COMMIT"
          echo "WORKSPACE is $env.WORKSPACE"
          sh "bash cloud-automation/gen3/bin/kube-roll-qa.sh"
        }
      }
    }
  }
  post {
    success {
      echo "https://jenkins.planx-pla.net/ $env.JOB_NAME pipeline succeeded"
    }
    failure {
      slackSend color: 'bad', message: "https://jenkins.planx-pla.net $env.JOB_NAME pipeline failed"
    }
    unstable {
      slackSend color: 'bad', message: "https://jenkins.planx-pla.net $env.JOB_NAME pipeline unstable"
    }
  }
}
