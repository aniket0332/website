pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:aniket_capstone23'
       appRegistry = "349818374164.dkr.ecr.ap-northeast-1.amazonaws.com/capstoneproject"
       capstoneRegistry = "https://349818374164.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "capstoneProject"
        service = "capstoneservice2"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/aniket0332/website'
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
            withAWS(credentials: 'aniket_capstone23', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
