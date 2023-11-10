pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '7479ff61-dc76-4a5b-9703-2da85acce88c', 
                url: 'https://github.com/4sudhakar/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t 4sudhakar/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push 4sudhakar/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '7479ff61-dc76-4a5b-9703-2da85acce88c', 
                url: 'https://github.com/4sudhakar/cicd-end-to-end/tree/main/deploy',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '7479ff61-dc76-4a5b-9703-2da85acce88c', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'admin')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/4sudhakar/cicd-end-to-end/tree/main/deploy HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
