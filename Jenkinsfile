pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }    
    stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar || true'
          sh 'cat target/sonar/report-task.txt || true'
        }
      }
    }
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.jar ubuntu@54.234.60.48:/home/ubuntu/project/apache-tomcat-9.0.62/webapps/framwork.jar'
              }      
           }       
    }
    
    
    stage ('DAST') {
      steps {
        sshagent(['ee3c29f7-1033-459f-a644-0d3c22982d78']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@54.234.60.48 "sudo docker run -t owasp/zap2docker-stable zap-baseline.py -t http://54.234.60.48:8080/framwork/" || true'
        }
      }
    }
    
  }
}
