currentBuild.displayName = "Final_Demo # "+currentBuild.number

   def getDockerTag(){
        def tag = sh script: 'git rev-parse HEAD', returnStdout: true
        return tag
        }
        

pipeline{
        agent any  
        environment{
	    Docker_tag = getDockerTag()
        }
        
        stages{
             stage('build')
                {
              steps{
                  script{
		  sh 'cp -r ../commit-job@2/target .'
                  sh 'docker build . -t yedluri/devops-training:$Docker_tag'
		  withCredentials([string(credentialsId: 'docker_password', variable: 'docker_password')]) {			    
				  sh 'docker login -u yedluri -p $docker_password'
				  sh 'docker push yedluri/devops-training:$Docker_tag'
			}
                       }
                    }
                 }		
		 
		stage('ansible playbook'){
			steps{
			 	script{
				    sh '''final_tag=$(echo $Docker_tag | tr -d ' ')
				     echo ${final_tag}test
				     sed -i "s/docker_tag/$final_tag/g"  deployment.yaml
				     '''
				    ansiblePlaybook become: true, credentialsId: '84e2e4dd-4e75-488d-8d21-2303f3855840', installation: 'ansible', inventory: 'hosts', playbook: 'ansible.yaml'
				}
			}
		}
               }           
}
