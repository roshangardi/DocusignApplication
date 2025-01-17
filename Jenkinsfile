pipeline
{
	agent any

	environment
		{
		registry = "public.ecr.aws/x4g8n8v4/docker-images-repo"
		registryCredential = 'dockerhub'
		}



	stages
	{
		stage('Cloning Git') {
	  steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/roshangardi/DevOps-Docusign-Application.git']]])
            }
		}

		stage('Building image') {
		  steps{
			script {
			  docker.build registry
			}
		  }
    }

		 // Uploading Docker images into AWS ECR
		stage('Pushing to ECR') {
		 steps{
			 script {
					sh '/usr/local/bin/aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x4g8n8v4'
					sh 'docker push public.ecr.aws/x4g8n8v4/docker-images-repo:latest'
			 }
			}
      }

		// Stopping Docker containers for cleaner Docker run
		stage('Stop Old containers') {
			 steps {
			 script {
				sh 'docker ps -f name=docusignapp -q | xargs --no-run-if-empty docker container stop'
				sh 'docker container ls -a -fname=docusignapp -q | xargs -r docker container rm'
			 }
		   }
		   }

		stage('Docker Run') {
		 steps{
			 script {
					sh 'docker run -d -p 5000:5000 --rm --name docusignapp public.ecr.aws/x4g8n8v4/docker-images-repo:latest'
				}
		  }
		  }

	  stage('Send Notifications') {
		steps {
		  mail bcc: '', body: '''This is a test email.
		This is to notify that the pipeline has reached the last stage.
		''', cc: '', from: '', replyTo: '', subject: 'Test pipeline jenkins email', to: 'roshan.gardi-sa@csulb.edu'
		}
	  }

	}

}
