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
                 withCredentials([usernamePassword(credentialsId: '114b914f-b8b1-4ef2-a1b4-93337429d550', passwordVariable: 'Password', usernameVariable: 'Username')]) {
                 sh "sudo docker login -u ${env.Username} -p ${env.Password}"
                 sh "sudo docker image tag mytomcat-image shama3541/mytomcat-image:test"
                 sh "sudo docker image push shama3541/mytomcat-image:test" 
               } 
             }  
          }
      stage('Deploy app') {
         steps {
            sh 'kubectl apply -f tomcat-app.yaml'
         }
      }
    }

  post {
    always {
      deleteDir() /* cleanup the workspace */
    }
  }
  }
