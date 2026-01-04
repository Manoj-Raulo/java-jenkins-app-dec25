pipeline {
 agent any

 environment {
        IMAGE_NAME = 'manoj900/springrestapi'
        PORT_MAPPING = '8081:7000'
        MINIKUBE_IP = '3.108.236.254'
        
        
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



 stage('Docker Login & Push') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'Docker-token',   // must match Jenkins credentials ID
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
                echo "Logging in to Docker Hub..."
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                echo "Pushing Docker image..."
                docker push ${IMAGE_NAME}:${BUILD_NUMBER}

                echo "Docker image pushed successfully"
            '''
        }
    }
}


stage("Clean the Docker Local Images"){
   steps {
         echo "run docker image prune -a -f command"
   }
 }

      stage('Connect to EC2 & Deployon on Minikube ') {
            steps {
                     
                    withCredentials([sshUserPrivateKey(credentialsId: 'ubuntu-cred', keyFileVariable: 'SSH_KEY')]) {
                    sh "ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ubuntu@$MINIKUBE_IP 'echo Connected to EC2'"
                    sh 'scp -i ${SSH_KEY} -o StrictHostKeyChecking=no deployment.yaml ubuntu@$MINIKUBE_IP:/home/ubuntu/'
                    sh 'ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ubuntu@$MINIKUBE_IP "kubectl delete -f /home/ubuntu/deployment.yaml --ignore-not-found=true"'
                    sh 'ssh -i ${SSH_KEY} -o StrictHostKeyChecking=no ubuntu@$MINIKUBE_IP "kubectl apply -f /home/ubuntu/deployment.yaml"'
                  }
          }
     }




 
} // end of stages

} // end of pipeline
