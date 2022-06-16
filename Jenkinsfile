pipeline {
  
  agent none
  
  environment {
    JENKINS_SERVICE_ACCOUNT = "jenkins-dev-serviceaccount"
    JENKINS_AGENT_CUSTOM_DOCKER_IMAGE = "dhillonfarms80/jenkins-inboundagent-kubectl:latest"
    JENKINS_CLOUD_NAME = "eksctl-june1-3"
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
      agent any
      steps {
        sh 'cat deployment.yaml'
      }
    }
    stage('Run K8s command') {
      agent {
        kubernetes {
          cloud "${JENKINS_CLOUD_NAME}"
          yaml '''
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                some-label: some-label-value
            spec:
              serviceAccountName: ${JENKINS_SERVICE_ACCOUNT}
              automountServiceAccountToken: true
              containers:
              - name: jnlp
                image: ${JENKINS_AGENT_CUSTOM_DOCKER_IMAGE}
                command:
                - /usr/local/bin/jenkins-agent
                tty: true
            '''
            }
        }
      steps {
        sh 'pwd'
        sh 'ls -ltr *'
        script {
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
        sh 'cat deployment.yaml'
        sh 'kubectl get pods -n ${NAMESPACE_NAME}'
        sh 'kubectl apply -f deployment.yaml'
        sh 'sleep 30'
        sh 'kubectl get pods -n ${NAMESPACE_NAME}'
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
