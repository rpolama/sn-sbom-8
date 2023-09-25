def artifactname = "artifact_devops_${env.BUILD_NUMBER}.jar"
def packageartifactname = "artifact_devops_${env.BUILD_NUMBER}.jar"
pipeline {
   agent any
   tools {
      maven 'Maven'
   }
   stages {
       stage("Build") {
           steps {
               echo "Building" 
               sh 'mvn -X clean install -DskipTests'
           }
       }

       stage("Test") {
           steps {
               echo "Testing"
       }

       stage("Generate SBOM with CycloneDX maven  plugin"){
           steps {
                 sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
           }
        }
        
      stage("Send SBOM to service now") {
         steps {
           script {
	       final def (String response, int code) = sh(script: "curl --location --request POST -w '\\n%{response_code}' 'https://empkgtokyo.service-now.com/api/sbom/core/upload' \
						--header 'Content-Type: application/json' \
						--header 'Authorization: Basic YWJlbC50dXRlcjpEZXZPcHMxIQ==' \
						--data-binary '@/var/jenkins_home/workspace/rpolama/target/bom.json'", returnStdout: true).trim()
						.tokenize("\n")

	       echo "HTTP response status code: $code"	
	       if (code == 200) {
                   echo response
		   def jsonSlurper = new groovy.json.JsonSlurper()
		   def object = jsonSlurper.parseText(response)
                   echo object.result.detail
		   echo object.result.status
		   echo object.result.sysid
                }
							  }
         }
      }

      stage("Deploy") {
             steps{
                  echo ">> Deploy in prod"
              }
      }      
      
  }
}
