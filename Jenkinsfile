def artifactname = "artifact_devops_${env.BUILD_NUMBER}.jar"
                   def packageartifactname = "artifact_devops_${env.BUILD_NUMBER}.jar"
pipeline {
    agent any
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
        }

        stage("Generate SBOM with CycloneDX maven  plugin") {
            steps {
                sh "mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom"
            }
        }

        stage("Send SBOM to service now") {
            steps {
                script {
                    final def (String response, String code) = sh(script: "curl --location --request POST -w '\\n%{response_code}' 'https://empkgtokyo.service-now.com/api/sbom/core/upload' \
						--header 'Content-Type: application/json' \
						--header 'Authorization: Basic YWJlbC50dXRlcjpEZXZPcHMxIQ==' \
						--data-binary '@target/bom.json'", returnStdout: true).trim()
                    .tokenize("\n")

                    echo "HTTP response status code: $code"
                    if (code == "200") {
                        def jsonSlurper = new groovy.json.JsonSlurper()
                        def object = jsonSlurper.parseText(response)
                        echo 'Response status: ' + object.result.status
			echo 'Response message: ' + object.result.message
                        echo 'Queue sys Id: ' + object.result.sysId
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                echo ">> Deploy in prod"
            }
        }

    }
}
