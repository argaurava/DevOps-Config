node('master') {
    
	notify('Project Build Started')

	try{
		stage('git checkout') {
			git 'https://github.com/argaurava/DevOps-Demo-Project.git'
		}    
    
			stage('Code Analysis' ) {
				sh 'mvn sonar:sonar'
			}
			
			stage('Build Automation') {    
				sh 'mvn clean package'
			}
			
			stage('Build Management'){
				def server = Artifactory.newServer url:'http://localhost:8081/artifactory', username: 'admin', password: 'password'
                            def uploadSpec = """{
                               "files": [
                                           {
                                             "pattern": "*.war",
                                             "target": "lib-staging"
                                           }
										]
                            }"""
			}

			stage('Deployment'){
              sh 'sudo cp target/*.war /root/Tomcat/apache-tomcat-8.5.37/webapps'
              sh 'sudo ls -ltr /root/Tomcat/apache-tomcat-8.5.37/webapps'
			}
			
			stage('Email Notification'){
				notify('Project Build Completed ')
			}

	}
	catch(err) {
		notify("Error ${err}")
		currentBuild.result='FAILURE'
	}

}
def notify(status){
    emailext(
        to: "arun.gaurav1989@gmail.com",
        subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' :</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
        
        )
}
