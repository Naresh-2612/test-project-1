pipeline {
agent any
stages {
stage('Build Application') {
steps {
bat 'mvn clean install'
}
}
stage('Test') {
steps {
echo 'Application in Testing Phase…'
bat 'mvn test'
}
}
// add this stage to your pipeline which uses simple groovy that can be improved according to the details required.
stage('Certificate Check') {
    steps {
        echo 'Finding certificate'
		script {
			
			//set the URL of mulesoft application which will parse the keytool output. Change it according to your application, the below might not be operational
			//final String url = "http://certification-cloudhub-api.us-e2.cloudhub.io/api/certficate"
			
			//find jks files in the workspace
			def files = findFiles(glob: '**/*.jks')
			
			//this focuses only on the first jks found with [0]. Please loop into all the files and do the below. 
			//When there are no certificate skip the below
			echo """${files[0].name} ${files[0].path}"""
			
			//certificate found, running keytool now. Please use Jenkins credentials to set the store password 
			def certDetails = bat(script : "keytool -list -v -keystore ${files[0].path} -storepass welcome1", returnStdout: true)
			//final String data = "${certDetails}"
			
			//comment the below later 
			echo "output : ${certDetails}"
			
			//set the fileName, appName, orgName and envName dynamically. Currently they are hardcoded.
            def response = bat(script: 'curl -X POST -H "Content-Type:application/java" -H "fileName:${files[0].name}" -H "appName:test-project-101" -H "envName:Sandbox" -H "orgName:Mulesoft" --data-raw \'${certDetails}\' http://certification-cloudhub-api.us-e2.cloudhub.io/api/certficate', returnStdout: true)
            echo response
			
	
   		}
	}

}
stage('Deploy CloudHub') {
environment {
ANYPOINT_CREDENTIALS = credentials('anypoint.credentials')
}
steps {
echo 'Deploying mule project due to the latest code commit…'
echo 'Deploying to the configured environment….'
 bat 'mvn package deploy -DmuleDeploy -Dusername=${ANYPOINT_CREDENTIALS_USR} -Dpassword=${ANYPOINT_CREDENTIALS_PSW}'
}
}





}
}