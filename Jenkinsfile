def app_url
def mvn_version

node('master') {
    
	notify('Project Build Started')
	

	try{
	
		def props_path="props_dir"
		sh 'ls -ltr'
		dir(props_path) {
			stage('git properties checkout') {
				git 'https://github.com/argaurava/DevOps-UTL.git'
			}
		}
		sh 'echo "checkout properies"'
		sh 'echo ${app_url} ${mvn_version}"'
		sh 'ls -ltr'
		props_file = readProperties file:'props_path/PropertiesFile.properties'
	
		app_url=props_file['APP_GIT_URL']
		mvn_version=props_file['MVN_PATH']
	
	
		stage('git checkout') {
			git url: "${app_url}"
		}
    
			stage('Code Analysis' ) {
				sh '${mvn_version}/mvn sonar:sonar'
			}
			
			stage('Build Automation') {    
				sh '${mvn_version}/mvn clean package'
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
