pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                url: 'https://github.com/vinayakentc/sample',
                branch: 'main'
           }
        }
        
        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t iamprabin/cicd:${BUILD_NUMBER} .
                    '''
                }
            }
        }
        
         stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push iamprabin/cicd:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                url: 'https://github.com/vinayakentc/argocd-my-app',
                branch: 'main'
            }
        }
        
        
     stage('Update K8S manifest & push to Repo'){
  steps {
    script{
        sh '''
        cat micro-app/microservice.yaml
        sed -i "s|image: docker.io/vinayakentc/nginx:[^ ]*|image: docker.io/vinayakentc/nginx:${BUILD_NUMBER}|g" micro-app/microservice.yaml
        cat micro-app/microservice.yaml
        git add micro-app/microservice.yaml
        git commit -m 'Updated the microservice.yaml | Jenkins Pipeline'
        git remote -v
     
       

        # Set the remote URL to use SSH
        git remote set-url origin git@github.com:vinayakentc/argocd-my-app.git
        
        '''
      }
    }
  }
}
}
  
}
