pipeline {
 agent any

 
  tools {
        maven 'maven-3.9.12'
    }

stages{

  
   stage("checkout"){
     steps {
           sh """
           echo "Checkout done - $PWD"
           ls -l
           """
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
