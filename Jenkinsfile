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
          jacoco execPattern: 'target/jacoco.exe'
         
         }
       
       }
        }  
 
     stage('Docker Build&Push') {
            steps {
              sh 'printenv'
              sh 'docker build -t issaouib/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push issaouib/numeric-app:""$GIT_COMMIT""'
            }
     }
    }
}
