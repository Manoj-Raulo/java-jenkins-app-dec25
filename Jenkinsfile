pipeline {
 agent any

 environment {
        IMAGE_NAME = 'manoj900/springrestapi'
        PORT_MAPPING = '8081:7000'
        
        
    }

 
  tools {
        maven 'maven-3.9.12'
    }

  parameters {
   string(name: 'DEPLOY_ENV', defaultValue: 'development', description: 'Select the target environment')
}

stages{

  
   stage("checkout"){
    when {
                // Execute this stage if the ENVIRONMENT parameter is 'development'
                expression { 
                     return params.DEPLOY_ENV == 'development' 
                }
          }
     steps {
           sh """
           echo "Checkout done - $PWD"
           echo "DEPLOY_ENV value $DEPLOY_ENV"
           ls -l
           echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL} from ${env.NODE_NAME}"
           """
      }
   } 

   stage("Check Tools") {
            steps {
                sh '''
                  echo "PATH = $PATH"
                  which mvn
                  mvn --version
                  java -version
                '''
            }
        }
       
    stage("Building the application"){
     steps {
         sh """
           echo "========Building Java Application============"
           mvn clean package -B -DskipTests
           echo "======Building Java Application completed====="
         """      
      }
    }

   stage("Testing the application"){
     steps {
         sh 'echo "========Testing Java Application============"'
         sh  '/opt/apache-maven-3.9.12/bin/mvn test'
          sh 'echo "========Completed Tests============"'
     }  
   }

 stage("Docker Image")
 {
   steps {
          sh """
           echo "========Building the Docker Image ============"
           docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} .
           echo "====== Building Image Completed ====="
         """      
   } 
 }



 stage('Docker Login') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            '''
        }
    }
}



 
} // end of stages

} // end of pipeline
