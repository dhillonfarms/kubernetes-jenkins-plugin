pipeline {
  agent none
  stages {
    stage('Run any command') {
        agent any
        steps {
          sh 'pwd'
          sh 'ls -ltr *'  
        }
    }
    stage('Run K8s command') {
      agent {
        kubernetes {
          cloud "eksctl-june1-2"
          yaml '''
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                some-label: some-label-value
            spec:
              serviceAccountName: jenkins-dev-serviceaccount
              automountServiceAccountToken: true
              containers:
              - name: jnlp
                image: dhillonfarms80/jenkins-inboundagent-kubectl:latest
                command:
                - /usr/local/bin/jenkins-agent
                tty: true
            '''
            }
        }
      steps {
        sh 'pwd'
        sh 'ls -ltr *'
        sh 'kubectl get pods -n dev'
        sh 'kubectl apply -f nginx.yaml'
        sh 'sleep 60'
        sh 'kubectl get pods -n dev'
        // container('k8scli') {
        //   sh 'ls'
        // }
      }
    }
  }
  post {
        always {
            echo 'This will always run no matter what'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}
