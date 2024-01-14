pipeline {
    tools {
        maven 'Maven3'
    }
    agent any
     environment {
        registryName = "springmxacrrepo"
        registryCredential = 'ACR'
        dockerImage = ''
        registryUrl = 'springmxacrrepo.azurecr.io'
    }
    stages {
        stage('checkout') {
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/theBikz/springboot-crud-docker.git']])
            }
        }
        stage ('build and push to ACR') {
            steps {
                script {
                  docker.withRegistry( "http://${registryUrl}", registryCredential ) {
                        sh "docker-compose build"
                        sh "docker-compose push"
                        
                }
                }
            }
        }
         stage ('K8S Deploy') {
          steps {
            script {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'K8S', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                    sh ('kubectl apply -f  app-claim0-persistentvolumeclaim.yaml,app-pod.yaml,app-service.yaml,db-persistentvolumeclaim.yaml,env-configmap.yaml,mysqldb-deployment.yaml,mysqldb-service.yaml')
                }
            }
        }
     }
    }
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}