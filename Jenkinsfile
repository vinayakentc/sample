pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '6f0aedbe-014a-4d3d-92ee-04d3277990d7', 
                url: 'https://github.com/vinayakentc/sample',
                branch: 'main'
           }
        }
        
        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    sudo docker build -t vinayakentc/nginx:${BUILD_NUMBER} .
                    '''
                }
            }
        }
        
         stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    sudo docker login -u "vinayakentc" -p "Ganesh@298" docker.io
                    sudo docker push vinayakentc/nginx:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'a35c4457-72fc-4444-8a87-56922662e74b', 
                url: 'https://github.com/vinayakentc/sample',
                branch: 'main'
            }
        }
        
        
     stage('Update K8S manifest & push to Repo'){
  steps {
    script{
      withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
        sh '''
        git config user.email "vinayakchavanentc@gmail.com"
        git config user.name "vinayakentc"

        cat sample/microservice.yaml
       # v=$(cat sample/microservice.yaml | grep image: vinayakentc)
        
        #sed 's/$v/image: vinayakentc/nginx:${BUILD_NUMBER}' sample/microservice.yaml
        sed -i 's+vinayakentc/nginx.*+vinayakentc/nginx:${BUILD_NUMBER}+g' sample/microservice.yaml

        cat sample/microservice.yaml
        git add sample/microservice.yaml
        git commit -m 'Updated the microservice.yaml | Jenkins Pipeline'     
       
        # Set the remote URL to use SSH
        git remote set-url origin git@github.com:vinayakentc/sample.git
        
        # Use ssh-agent to add the SSH key and push the changes
        #git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/sample.git HEAD:main
        '''
      }
    }
  }
}
}
}    
