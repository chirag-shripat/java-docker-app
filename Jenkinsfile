pipeline {
	agent any
	stages {
		stage("SCM") {
			steps {
				git 'https://github.com/chirag-shripat/java-docker-app.git'
				}
			}

		stage("build") {
			steps {
				sh 'sudo mvn dependency:purge-local-repository'
				sh 'sudo mvn clean package'
				}
			}
		stage("Image") {
			steps {
				sh 'sudo docker build -t java-repo-img:v1 .'
              			sh 'sudo docker tag java-repo-img:v2 chiragshripat/java-pipeline:latest'
				}
			}
				
	
		stage("Docker Hub") {
			steps {
			     withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'password', usernameVariable: 'username')]) {
              		     sh 'sudo docker login -u ${username} -p ${password}'
              		     sh 'sudo docker push chiragshripat/java-pipeline:latest'
				}
			}	

		}
		stage("QAT Testing") {
			steps {
				sh 'sudo docker rm -f $(sudo docker ps -a -q)'
              			sh 'sudo docker run -dit -p 8081:8080 --name javaa-web-app chiragshripat/java-pipeline:latest'
				}
			}
		stage("testing website") {
			steps {
				retry(5) {
				sh 'curl --silent http://65.2.140.187:8080/java-web-app/ '
					}
				}
			}

		stage("Approval status") {
			steps {
				script {
					 Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput
					}
				}	
		}
		stage("Prod Env") {
			steps {
			 sshagent(['ubuntu']) {
			    sh 'ssh -o StrictHostKeyChecking=no ec2-user@43.204.98.132 sudo docker rm -f $(sudo docker ps -a -q)' 
	                    sh "ssh -o StrictHostKeyChecking=no ec2-user@43.204.98.132 sudo docker run  -d  -p  49153:8080  chiragshripat/java-pipeline:latest"
				}
			}
		}
	}
}

