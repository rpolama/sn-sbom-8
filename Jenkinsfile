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
               //sh 'mvn test'
           }
//           post {
//                 always {
//                     junit '**/target/surefire-reports/*.xml' 
//                 }
//           }
        }
      
//       stage("Create Artifact for prod") {
//            steps {
//               echo "Creating artifact"
//               sh 'mvn package'
//               sleep 3
//               snDevOpsArtifact(artifactsPayload:"""
//                {"artifacts": 
//                   [
//                      {
//                         "name": "JenkinsDevOpsProject-mvp.jar",
//                         "version":"0.${env.BUILD_NUMBER}.0",
//                         "semanticVersion": "0.${env.BUILD_NUMBER}.0",
//                         "repositoryName": "JenkinsDevOpsProject"
//                        }
//                     ]
//                  }""")
//               snDevOpsPackage(name: "JenkinsDevOpsProject-package", artifactsPayload: """
//               {"artifacts": 
//                [
//                   {
//                      "name": "JenkinsDevOpsProject-mvp.zip",
//                      "repositoryName": "JenkinsDevOpsProject",
//                      "pipelineName": "SunilPipeline",
//                      "taskExecutionNumber":"${env.BUILD_NUMBER}",
//                      "stageName":"Create Artifact for prod",
//                      "branchName": "main"
//                    }
//                  ]
//                 }""")
//            }
//         }
      
        stage("Generate SBOM with CycloneDX maven  plugin"){
           steps {
                 sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
           }
         
        }
        
      stage("Send SBOM to service now") {
         steps {
           script {

                    final def (String response, int code) = sh(script: "curl --location --request POST -w '\\n%{response_code}' 'https://devopssbom.service-now.com/api/sn_sbom/parser_api/parse' \
						--header 'Content-Type: application/json' \
						--header 'Authorization: Basic YWRtaW46U2VydmljZW5vdzEyMyE=' \
						--data-binary '@/var/jenkins_home/workspace/sn-sbom-pipeline/target/bom.json'", returnStdout: true).trim()
						.tokenize("\n")

					echo "HTTP response status code: $code"
					if (code == 200) {
                    	echo response
			def jsonSlurper = new JsonSlurper()
			def object = jsonSlurper.parseText(response)
                        echo object.detail
			echo object.status
                    }
                }
         }
      }
//       stage("Deploy") {
//              steps{
//                   snDevOpsChange()
//                   echo ">> Deploy in prod"
//               }
//       }      
      
  }
}
