pipeline {
    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
    }

    stages {

        stage('Sonar quality check'){
            agent{
                docker{
                    image 'maven'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'mvn clean package sonar:sonar'
    
                  }

                }
            }

        }
        stage('Quality Gate status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('docker build & push to nexus repo'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
   


                    sh '''
                     docker build -t 52.87.254.15:8083/springapp:${VERSION} .

                     docker login -u admin -p $nexus_creds 52.87.254.15:8083

                     docker push 52.87.254.15:8083/springapp:${VERSION}

                     docker rmi 52.87.254.15:8083/springapp:${VERSION}
                    '''
                }
                }
            }
        }
//         stage('Identifying misconfigs using datree in helm charts'){
//             steps{
//                 script{
//                     dir('kubernetes/myapp/') {
//                         sh 'helm datree test .'
    
// }
//                 }
//             }
//         }
    }
}
