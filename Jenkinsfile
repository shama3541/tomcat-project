pipeline {
   environment {
     git_url = "https://github.com/salilkul/tomcat-project.git"
     git_branch = "master"
   }

  agent any
 
  stages {
    stage('Pull Source') {
      steps {
        git credentialsId: 'ff6d7224-fa35-465b-a41b-93ff8a6d2663', branch: "${git_branch}", url: "${git_url}"
       
      }
     }
    
    stage('Maven Build') {
     steps { 
          sh "if [ -f \"pom.xml\" ];then mvn -B -f pom.xml clean package && cp target/*.jar .;else echo \"This is not a Java Project\";fi"
     }
    }
     
     stage('Docker Image Build') {     
        steps {
              sh 'sudo docker build -t mytomcat-image . '
               }
             }
        stage('Docker image push') {
           steps {
                 withCredentials([usernamePassword(credentialsId: 'b2674f2b-668e-4af0-9b33-dc80948a6230', passwordVariable: 'Password', usernameVariable: 'Username')]) {
                 sh "sudo docker login -u ${env.Username} -p ${env.Password}"
                 sh "sudo docker image tag mytomcat-image salilkul87/mytomcat-image:test"
                 sh "sudo docker image push salilkul87/mytomcat-image:test" 
               } 
             }  
          }
      stage('Deploy app') {
         steps {
            sh 'kubectl apply -f tomcat-deploy.yaml'
            sh 'sleep 60'
            sh 'kubectl apply -f tomcat-svc.yaml'
         }
      }
    }

  post {
    always {
      deleteDir() /* cleanup the workspace */
    }
  }
  }
