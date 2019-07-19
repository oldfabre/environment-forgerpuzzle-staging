pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    label "jenkins-maven"
  }
  environment {
    DEPLOY_NAMESPACE = "jx-staging"
  }
  stages {
    stage('Validate Environment') {
      steps {
        container('maven') {
          dir('env') {
            sh 'jx step helm build'
          }
        }
      }
    }
    stage('Update Environment') {
      when {
        branch 'master'
      }
      steps {
        container('maven') {
          dir('env') {
            sh 'helm upgrade --namespace jx-staging --install --wait --force --timeout 1200 --values values.yaml jx-staging .'
          }
        }
      }
    }
    stage('Deploy preview env') {
      when {
        branch 'PR-*'
      }
      steps {
        container('maven') {
          dir('env') {
            sh 'jx preview --name jx-preview --namespace jx-preview'
          }
        }
      }
    }
    stage('Run DDT') {
      steps {
        container('maven') {
          dir('env') {
            sh 'kubectl delete po jx-staging-ddt-test -n jx-staging || true'
            sh 'helm test jx-staging --timeout 1800'
          }
        }
      }
    }
  }
}
