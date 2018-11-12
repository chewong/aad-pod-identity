pipeline {
    agent {
        docker {
            image 'microsoft/azure-cli'
            args '-u root:root'
        }
    }
    environment {
        KUBECONFIG = credentials("${KUBECONFIG}")
    }
    stages {
        stage('Setup azure cli and kubectl') {
            steps {
                withCredentials([azureServicePrincipal("${SP_CREDENTIAL}")]) {
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                }
                sh 'az aks install-cli'
                sh 'kubectl cluster-info'
                sh 'apk upgrade && apk update && apk add --no-cache go make'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'make unit-test || true'
            }
        }

        stage('Create a k8s cluster with acs-engine') {
            steps {
                sh 'make e2e'
            }
        }
    }
}