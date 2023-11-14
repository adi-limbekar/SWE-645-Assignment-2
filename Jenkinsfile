pipeline {
   environment {
        registry = "adi0222/studentSurveyForm"
        registryCredential = 'Georgemason1'
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
   agent any

   stages {
      stage('Build') {
         steps {
            script{
               checkout scm
               sh 'rm -rf *.war'
               sh 'jar -cvf studentSurveyForm.war -C src/main/webapp/ .'
               sh 'echo ${BUILD_TIMESTAMP}'
               docker.withRegistry('',registryCredential){
                  def customImage = docker.build("adi0222/studentsurveyform:${env.TIMESTAMP}")
               }
            }
         }
      }

      stage('Push Image to Dockerhub') {
         steps {
            script{
               docker.withRegistry('',registryCredential){
                  sh "docker push adi0222/studentsurveyform:${env.TIMESTAMP}"
               }
            }
         }
      }

      stage('Deploying to Rancher to single node(deployed in 3 replicas)') {
         steps {
            script{
               sh "kubectl set image deployment/deploymentone container-0=adi0222/studentsurveyform:${env.TIMESTAMP}"
            }
         }
      }

      stage('Deploying to Rancher using Load Balancer as a service') {
         steps {
            script{
               sh "kubectl set image deployment/deploymentone-lb container-0=adi0222/studentsurveyform:${env.TIMESTAMP}"
            }
         }
      }
   }
}
