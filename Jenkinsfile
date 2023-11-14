pipeline {
   environment {
        registry = "adi0222/studentSurveyForm"
        registryCredential = 'DockerHubCredentials'
        TIMESTAMP = new Date().toString()
        KUBECONFIG_CREDENTIALS_ID = 'K8scluster'
        DOCKER_IMAGE = "adi0222/surveyformimage:${env.TIMESTAMP}"
    }
   agent any

   stages {
      stage('Build') {
         steps {
            script{
               checkout scm
               sh 'rm -rf *.war'
               sh 'jar -cvf studentsurveyForm.war -C src/main/webapp/ .'
               sh 'echo ${BUILD_TIMESTAMP}'
               docker.withRegistry('',registryCredential){
                  def customImage = docker.build("$DOCKER_IMAGE")
               }
            }
         }
      }

      stage('Push Image to Dockerhub') {
         steps {
            script{
               docker.withRegistry('',registryCredential){
                  sh "docker push $DOCKER_IMAGE"
               }
            }
         }
      }

      stage('Deploying to Rancher as Load Balancer') {
            steps {
                withCredentials([file(credentialsId: "$KUBECONFIG_CREDENTIALS_ID", variable: 'KUBECONFIG')]) {
                    sh 'kubectl set image deployment/surveyformlb container-0=$DOCKER_IMAGE'
                }
            }
        }

        stage('Deploying to Rancher as Node Port') {
            steps {
                withCredentials([file(credentialsId: "$KUBECONFIG_CREDENTIALS_ID", variable: 'KUBECONFIG')]) {
                    sh 'kubectl set image deployment/surveyformnp container-0=$DOCKER_IMAGE'
                }
            }
        }
   }
}
