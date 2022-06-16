pipeline {
  
  agent none
  
  environment {
    DEPLOYMENT_NAME = "nginx-deploy"
    NAMESPACE_NAME = "dev"
    APP_LABEL_VALUE = "nginx-server"
    REPLICA_COUNT = "2"
    CONTAINER_NAME = "nginx"
    CONTAINER_IMAGE = "nginx:1.14.2"
    CONTAINER_PORT = "80"
  }
  
  stages {
    stage('Run any command') {
        agent any
        steps {
          sh 'pwd'
          sh 'ls -ltr *'  
        }
    }
    stage('Create deployment file'){
      steps {
        def text = readFile file: "deployment.yaml"
        text = text.replaceAll("DEPLOYMENT_NAME", "${DEPLOYMENT_NAME}")
        text = text.replaceAll("NAMESPACE_NAME", "${NAMESPACE_NAME}")
        text = text.replaceAll("APP_LABEL_VALUE", "${APP_LABEL_VALUE}")
        text = text.replaceAll("REPLICA_COUNT", "${REPLICA_COUNT}")
        text = text.replaceAll("CONTAINER_NAME", "${CONTAINER_NAME}")
        text = text.replaceAll("CONTAINER_IMAGE", "${CONTAINER_IMAGE}")
        text = text.replaceAll("CONTAINER_PORT", "${CONTAINER_PORT}")
        writeFile file: "deployment.yaml", text: text
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
        sh 'cat deployment.yaml'
//         sh 'kubectl get pods -n dev'
//         sh 'kubectl apply -f deployment.yaml'
//         sh 'sleep 60'
//         sh 'kubectl get pods -n dev'
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
