pipeline {

  agent any

  stages {

    stage('build') {

      steps {

        sh "mvn --version"
        sh 'mvn -B -DskipTests clean package'

        
      }
    }
    
    stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    stage('deply') {
      
      steps {
        
        sh "echo deploying!"
      }
    }
  }
}
