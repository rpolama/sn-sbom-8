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
  
//       stage("Deploy") {
//              steps{
//                   snDevOpsChange()
//                   echo ">> Deploy in prod"
//               }
//       }      
      
  }
}
