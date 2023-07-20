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

                    final def (String response, int code) = sh(script: "curl --location --request POST -w '\\n%{response_code}' 'https://empkgtokyo.service-now.com/api/sbom/core/upload' \
						--header 'Content-Type: application/json' \
						--header 'Authorization: Basic YWJlbC50dXRlcjpEZXZPcHMxIQ==' \
						--data-binary '@/var/jenkins_home/workspace/Mohan-SBOM/target/bom.json'", returnStdout: true).trim()
						.tokenize("\n")

					echo "HTTP response status code: $code"
			
 
			def fileContent = new File('/var/jenkins_home/workspace/Mohan-SBOM/target/bom.json').text             

def jsonSlurper = new groovy.json.JsonSlurper()             

def object = jsonSlurper.parseText(fileContent)             

echo "${object}"
			if (code == 200) {
                    	echo response
			def jsonSlurper = new groovy.json.JsonSlurper()
			def object = jsonSlurper.parseText(response)
			artifactname = artifactname+"#"+object.result.sysid
                        echo object.result.detail
			echo object.result.status
			echo object.result.sysid
			echo artifactname
                    }
                }
	      echo "Creating artifact in service now"
              //snDevOpsArtifact(artifactsPayload:"""
               //{"artifacts": 
                 // [
                   //  {
                     //   "name": "${artifactname}",
                       // "version":"0.${env.BUILD_NUMBER}.0",
                     //   "semanticVersion": "0.${env.BUILD_NUMBER}.0",
                       // "repositoryName": "DevOpsSbom"
                   //    }
                   // ]
//                 }""")
  //            snDevOpsPackage(name: "DevOpsSbom-package", artifactsPayload: """
    //          {"artifacts": 
      //         [
        //          {
          //           "name": "${packageartifactname}",
            //         "repositoryName": "DevOpsSbom",
		//     "version":"0.${env.BUILD_NUMBER}.0",
          //           "taskExecutionNumber":"${env.BUILD_NUMBER}",
            //         "branchName": "main"
              //     }
            //     ]
            //    }""")
         }
      }
      stage("Deploy") {
             steps{
                  //snDevOpsChange()
                  echo ">> Deploy in prod"
              }
      }      
      
  }
}
