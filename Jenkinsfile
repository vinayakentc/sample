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
                    sudo apt-get update
                    apt install sudo
                    sudo apt-get install wget
                    sudo wget http://get.docker.com/builds/Linux/x86_64/docker-latest.tgz
                    && sudo tar -xvzf docker-latest.tgz
                    && sudo mv docker/* /usr/bin/
                    && sudo rm -r docker docker-17.04.0-ce.tgz
                    echo 'Buid Docker Image'
                    docker build -t vinayakentc/nginx:${BUILD_NUMBER} .
                    '''
                }
            }
        }
        
         stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push vinayakentc/nginx:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'a35c4457-72fc-4444-8a87-56922662e74b', 
                url: 'https://github.com/vinayakentc/argocd-my-app',
                branch: 'main'
            }
        }
        
        
     stage('Update K8S manifest & push to Repo'){
  steps {
    script{
      withCredentials([sshUserPrivateKey(credentialsId: 'e332e937-0f41-4fc1-8532-2d5506a6d785', keyFileVariable: 'SSH_KEY_FILE', passphraseVariable: 'SSH_PASSPHRASE', usernameVariable: 'SSH_USERNAME')]) {
        sh '''
        cat microservice.yaml
        sed -i "s|image: docker.io/vinayakentc/nginx:[^ ]*|image: docker.io/vinayakentc/nginx:${BUILD_NUMBER}|g" microservice.yaml
        cat microservice.yaml
        git add microservice.yaml
        git commit -m 'Updated the microservice.yaml | Jenkins Pipeline'
        git remote -v
     
       

        # Set the remote URL to use SSH
        git remote set-url origin git@github.com:vinayakentc/argocd-my-app.git
        
        # Use ssh-agent to add the SSH key and push the changes
        ssh-agent bash -c 'ssh-add ${SSH_KEY_FILE}; git push origin HEAD:main'
        '''
      }
    }
  }
}
}
}    
