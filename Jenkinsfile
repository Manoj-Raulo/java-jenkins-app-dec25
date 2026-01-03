pipeline {
 agent any

stages{ 
       
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
