pipeline {
     agent any
        environment {
        registryName = "bhashini"
        registryCredential = 'ACR'
        dockerImage = 'vc'
        registryUrl = 'bhashini.azurecr.io'
    }
    stages {
        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sonulodha/appservice']]])
            }
            }
        stage ('build image') {
            steps {        
                script {
                    dockerImage = docker.build("${registryName}:${env.BUILD_ID}")
                     }      
                }
            }
   // Uploading Docker images into ACR
    stage('push to ACR') {
     steps{   
         script {
            docker.withRegistry( "http://${registryUrl}", registryCredential ) {
            dockerImage.push()
            }
        }
      }
    }
    
    stage('deploy to appservice') {
        steps {
            withCredentials([
            string(credentialsId: 'app-id', variable: 'username'),
            string(credentialsId: 'tenant-id', variable: 'tenant'),
            string(credentialsId: 'app-id-pass', variable: 'password')
            ]) {
            sh """
                /var/lib/jenkins/.local/bin/az login --service-principal -u ${username} -p ${password} --tenant ${tenant}
                /var/lib/jenkins/.local/bin/az  webapp config container set --name macbookapp --resource-group app-service  --docker-custom-image-name=bhashini.azurecr.io/bhashini:${env.BUILD_ID}
                """
            }
        }
    }
    
}
        
}
