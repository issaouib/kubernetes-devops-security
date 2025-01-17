pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
     stage('Unit Test --- Junit and Jacoco') {
            steps {
              sh "mvn test"
            }
       post {
         always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
         
         }
       
       }
        }  
 stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
      post {
        always {
          pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
      }
    }
     stage('Docker Build&Push') {
            steps {
              withDockerRegistry(credentialsId: 'docker', url: "") {
              sh 'printenv'
              sh 'docker build -t issaouib/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push issaouib/numeric-app:""$GIT_COMMIT""'
              }
            }
     }
    stage('kubernetes Deployments') {
      steps {
        withKubeConfig(credentialsId: 'kubeconfig') {
          sh "sed -i 's#replace#issaouib/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
      }
    }
    }
    }
}
