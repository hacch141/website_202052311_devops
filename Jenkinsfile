pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:202052311_CapStone'
       appRegistry = "006661006390.dkr.ecr.us-east-1.amazonaws.com/capstone_202052311"
       capstoneRegistry = "https://006661006390.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "202052311_CapStone_cluster"
        service = "202052311_service_2"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/hacch141/website_202052311_devops'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202052311_CapStone', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
