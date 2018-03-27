// comment
pipeline {
 agent any
 stages {
        stage('Checkout-git'){
               steps{
		git poll: true, url: 'https://github.com/subhamproject/test-jenkins-1.git'
               }
        }
        stage('CreateVirtualEnv') {
            steps {
				sh '''
					bash -c "virtualenv entorno_virtual && source entorno_virtual/bin/activate"
				'''

            }
        }
        stage('InstallRequirements') {
            steps {
            	sh '''
            		bash -c "source ${WORKSPACE}/entorno_virtual/bin/activate && ${WORKSPACE}/entorno_virtual/bin/python ${WORKSPACE}/entorno_virtual/bin/pip install -r requirements.txt"
                '''
            }
        }   
        stage('TestApp') {
            steps {
            	sh '''
            		bash -c "source ${WORKSPACE}/entorno_virtual/bin/activate &&  cd src && ${WORKSPACE}/entorno_virtual/bin/python ${WORKSPACE}/entorno_virtual/bin/pytest && cd .."
                '''
            }
        }  
        stage('RunApp') {
            steps {
            	sh '''
            		bash -c "source entorno_virtual/bin/activate ; ${WORKSPACE}/entorno_virtual/bin/python src/main.py &"
                '''
            }
        } 
        stage('BuildDocker') {
            steps {
            	sh '''
            		docker build -t apptest:latest.$GIT_COMMIT .
                '''
            }
        } 
    stage('PushDockerImage') {
            steps {
            	sh '''
            		docker tag apptest:latest.$GIT_COMMIT subhammandal/test-jenkins-1.$GIT_COMMIT
					docker push subhammandal/test-jenkins-1.$GIT_COMMIT
					docker rmi apptest:latest.$GIT_COMMIT
                '''
            }
     } 
 }
	 post {
    success {
      // notify users when the Pipeline fails
      mail to: 'smandal@rythmos.com',
          subject: "Sucessfully Completed: ${currentBuild.fullDisplayName}",
          body: "Successfully Completed ${env.BUILD_URL}"
     }
    
	failure {
      // notify users when the Pipeline fails
     // mail to: 'smandal@rythmos.com',
     //     subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
       //   body: "Something is wrong with ${env.BUILD_URL}"
	//slackSend (color: '#FFFE89', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
       emailext (
          subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
          body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
          <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
          attachLog: true, compressLog: true,
          recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'DevelopersRecipientProvider'],
                               [$class: 'RequesterRecipientProvider']]
        )
     }
    }
  }


