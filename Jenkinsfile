pipeline{
    agent any
    environment{
        TAG = "${getTag()}"
    }
    
    stages{
        stage('code-checkout'){
            steps{
                git credentialsId: 'github-cred', url: 'https://github.com/shobhadpr/nodeapp_test.git'
            }
        }
        stage('Build-image'){
            steps{
                sh "docker build . -t 776550/nodeapp:${env.TAG}"
            }
        }
        stage('docker-push'){
            environment{
             registryCredential ='docker-hub-cred'   
            }
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', passwordVariable: 'pswd', usernameVariable: 'user')]) {
                       sh "docker login -u ${user} -p ${pswd}"
                    }
                    sh "docker push 776550/nodeapp:${env.TAG}"
                    
                    
                }
                
            }
        }
    
        
        stage ('K8S Deploy') {
            steps{
              script{
                 
                  kubernetesDeploy (configs: 'deploymentservice.yml',kubeconfigId: 'kubeconfig',enableConfigSubstitution: true)  
                }  
            }
       
                
                                  
        }
    }
}
def getTag(){
    def tag = sh returnStdout: true, script: 'git log --oneline -1 | awk \'{print $1}\''
    return tag
}
