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
    stage('Run DDT') {
      steps {
        container('maven') {
          dir('env') {
            sh 'kubectl delete po jx-preview-ddt-test -n jx-preview || true'
            sh 'helm test jx-preview --timeout 1800'
          }
        }
      }
    }
  }
}
