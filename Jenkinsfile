pipeline {
    agent any
	
	environment {
		PROJECT_ID = 'possible-sun-342923'
    CLUSTER_NAME = 'cluster-1'
    LOCATION = 'us-central1-c'
    CREDENTIALS_ID = 'kubernetes'
    EMAIL_TO = 'bermcis6@gmail.com'
	}
	
    stages {	    
	    
	    stage('Test') {
		    steps {
			    echo 'Test'
			   // sh 'dotnet test'
		    }
	    }

       stage('Sonar Qube') {
		    steps {
			    echo 'Sonar'
			   // sh 'mvn test'
		    }
	    }
	    
	    stage('Build Docker Image') {
		    steps {
			     script {
				     myimage = docker.build("raghukom/walmartdotnet:${env.BUILD_ID}")				
			     }
           //	sh 'docker build -t walmartpayment:1.0.0 .'
		    }
	    }
	    
	    stage("Push Docker Image") {
		    steps {
				 //sh 'echo $DOCKERCREDS_PSW | docker login -u $DOCKERCREDS_USR --password-stdin'
				 //sh 'docker push raghukom/devops:latest'
			     script {
				     echo "Push Docker Image"
				     withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'username', passwordVariable: 'pwd')]) {
                      sh 'docker login -u $username -p $pwd'
              }
				    myimage.push("${env.BUILD_ID}")			    
			     }
		    }
	    }
	    
	    stage('Deploy to K8s Dev') {
		    steps{
			    echo "Deployment started ..."

			     sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
			    //  sh "sed -i 's/amerisourcebergenapp/amerisourcebergenapp-dev/g' deployment.yaml"

			    // sh 'cat deployment.yaml'
				  echo "Start deployment of deployment.yaml"
				  step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
			    echo "Deployment Finished ..."
		    }
	    }	    
    }

    post {
        failure {
            emailext body: 'Check console output at $BUILD_URL to view the results. \n\n ${CHANGES} \n\n -------------------------------------------------- \n${BUILD_LOG, maxLines=100, escapeHtml=false}', 
                    to: "${EMAIL_TO}", 
                    subject: 'Build failed in Jenkins: $PROJECT_NAME - #$BUILD_NUMBER'
        }
        always {
            echo 'always'
        }
        success {
            echo 'success'
        }
    }
}
