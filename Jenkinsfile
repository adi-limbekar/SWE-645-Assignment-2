ypipeline {
   environment {
        registry = "adi0222/studentSurveyForm"
        registryCredential = 'DockerHubCredentials'
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

      stage('Deploying to Rancher using Node port as a service') {
         steps {
            script{
               sh "kubectl set image deployment/surveyformnp container-0=adi0222/studentsurveyform:${env.TIMESTAMP}"
            }
         }
      }

      stage('Deploying to Rancher using Load Balancer as a service') {
         steps {
            script{
               sh "kubectl set image deployment/surveyformlb container-0=adi0222/studentsurveyform:${env.TIMESTAMP}"
            }
         }
      }
   }
}
