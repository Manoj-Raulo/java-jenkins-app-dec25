pipeline {
 agent any

 
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
           mvn clean package
           echo "======Building Java Application completed====="
         """      
      }
    }            


} // end of stages

} // end of pipeline
