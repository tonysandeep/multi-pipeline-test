pipeline {
    agent { label 'master' }
	options { skipDefaultCheckout() }
    stages {
	 
                stage("Checkout SCM") {
					steps {
								cleanWs()
								echo 'checkout scm'
								checkout scm
						}
				}
		stage("Unit Test") {
					agent { label 'windows' }
					steps {
								cleanWs()
								checkout scm
								echo ' Unit Test Stage'
								bat 'mvn test'
								junit 'target\\surefire-reports\\*.xml'	
						}
				}
	    			stage('Scanning for Security') {
					 steps {
						fodStaticAssessment bsiToken: 'eyJ0ZW5hbnRJZCI6OTkyNCwidGVuYW50Q29kZSI6IkRYQ18xODAxX0ZNQV85MjY4ODYyMzciLCJyZWxlYXNlSWQiOjgyMTgyLCJwYXlsb2FkVHlwZSI6IkFOQUxZU0lTX1BBWUxPQUQiLCJhc3Nlc3NtZW50VHlwZUlkIjoxNCwidGVjaG5vbG9neVR5cGUiOiJKQVZBL0oyRUUiLCJ0ZWNobm9sb2d5VHlwZUlkIjo3LCJ0ZWNobm9sb2d5VmVyc2lvbiI6IjEuOCIsInRlY2hub2xvZ3lWZXJzaW9uSWQiOjEyLCJhdWRpdFByZWZlcmVuY2UiOiJBdXRvbWF0ZWQiLCJhdWRpdFByZWZlcmVuY2VJZCI6MiwiaW5jbHVkZVRoaXJkUGFydHkiOmZhbHNlLCJpbmNsdWRlT3BlblNvdXJjZUFuYWx5c2lzIjpmYWxzZSwicG9ydGFsVXJpIjoiaHR0cHM6Ly90cmlhbC5mb3J0aWZ5LmNvbS8iLCJhcGlVcmkiOiJodHRwczovL2FwaS50cmlhbC5mb3J0aWZ5LmNvbSIsInNjYW5QcmVmZXJlbmNlIjoiU3RhbmRhcmQiLCJzY2FuUHJlZmVyZW5jZUlkIjoxfQ==', entitlementPreference: 'SingleScanOnly', inProgressScanActionType: 'CancelInProgressScan', overrideGlobalConfig: true, personalAccessToken: 'fortifyondemand', remediationScanPreferenceType: 'RemediationScanIfAvailable', srcLocation: '.', tenantId: 'DXC_1801_FMA_926886237', username: 'tonisundhp@gmail.com'
						fodPollResults bsiToken: 'eyJ0ZW5hbnRJZCI6OTkyNCwidGVuYW50Q29kZSI6IkRYQ18xODAxX0ZNQV85MjY4ODYyMzciLCJyZWxlYXNlSWQiOjgyMTgyLCJwYXlsb2FkVHlwZSI6IkFOQUxZU0lTX1BBWUxPQUQiLCJhc3Nlc3NtZW50VHlwZUlkIjoxNCwidGVjaG5vbG9neVR5cGUiOiJKQVZBL0oyRUUiLCJ0ZWNobm9sb2d5VHlwZUlkIjo3LCJ0ZWNobm9sb2d5VmVyc2lvbiI6IjEuOCIsInRlY2hub2xvZ3lWZXJzaW9uSWQiOjEyLCJhdWRpdFByZWZlcmVuY2UiOiJBdXRvbWF0ZWQiLCJhdWRpdFByZWZlcmVuY2VJZCI6MiwiaW5jbHVkZVRoaXJkUGFydHkiOmZhbHNlLCJpbmNsdWRlT3BlblNvdXJjZUFuYWx5c2lzIjpmYWxzZSwicG9ydGFsVXJpIjoiaHR0cHM6Ly90cmlhbC5mb3J0aWZ5LmNvbS8iLCJhcGlVcmkiOiJodHRwczovL2FwaS50cmlhbC5mb3J0aWZ5LmNvbSIsInNjYW5QcmVmZXJlbmNlIjoiU3RhbmRhcmQiLCJzY2FuUHJlZmVyZW5jZUlkIjoxfQ==', overrideGlobalConfig: true, personalAccessToken: 'fortifyondemand', pollingInterval: 1, tenantId: 'DXC_1801_FMA_926886237', username: 'tonisundhp@gmail.com'
					}
				}
	    
				stage('Build and Package') {
						steps {
								echo 'Clean Build'
								sh "ls"
								sh "pwd"
								sh "mvn sonar:sonar clean compile package -Dtest=\\!TestRunner* -DfailIfNoTests=false -Dsonar.projectKey=addressbook -Dsonar.host.url=http://10.62.125.9:8085/ -Dsonar.login=f16fabd2605044f38e79e4c0e4bc5f73c55dd144"
								//sh "mvn sonar:sonar clean compile -Dtest=\\!TestRunner* -DfailIfNoTests=false -Dsonar.projectKey=employee_jdbc -Dsonar.host.url=http://34.93.123.206/ -Dsonar.login=aac7cc7809ddc82ce0070e3f74726c71216936b6"
								//sh 'mvn clean compile' 
						}
				}

				/*stage('Package') {
				    steps {
					echo 'Packaging'
					sh 'mvn package -DskipTests'
				    }
				}*/
				stage("publish to nexus") {
					steps {
						echo 'publish to nexus'
						script {
							// Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
							pom = readMavenPom file: "pom.xml";
							// Find built artifact under target folder
							filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
							// Print some info from the artifact found
							echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
							// Extract the path from the File found
							artifactPath = filesByGlob[0].path;
							// Assign to a boolean response verifying If the artifact name exists
							artifactExists = fileExists artifactPath;
							if(artifactExists) {
								echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
								nexusArtifactUploader(
									nexusVersion: NEXUS_VERSION,
									protocol: NEXUS_PROTOCOL,
									nexusUrl: NEXUS_URL,
									groupId: pom.groupId,
									version: pom.version,
									repository: NEXUS_REPOSITORY,
									credentialsId: NEXUS_CREDENTIAL_ID,
									artifacts: [
										// Artifact generated such as .jar, .ear and .war files.
										[artifactId: pom.artifactId,
										classifier: '',
										file: artifactPath,
										type: pom.packaging]//,
										// Lets upload the pom.xml file for additional information for Transitive dependencies
									]
								);
							} else {
								error "*** File: ${artifactPath}, could not be found";
							}
							
						}
					}
				}
						stage('Deploy') {
						    steps {
							sh 'curl --upload-file target/addressbook.war "http://tomcat:password@10.62.125.9:8083/manager/text/deploy?path=/addressbook&update=true"'
							//sh 'curl --upload-file target/addressbook.war "http://tomcat:password@34.93.238.186:8081/manager/text/deploy?path=/addressbook&update=true"'
							//withCredentials([usernamePassword(credentialsId: 'nexusadmin', passwordVariable: 'pass', usernameVariable: 'user')]) {
							//    sh 'curl --upload-file target/hello-world-war-1.0.0-SNAPSHOT.war "http://${user}:${pass}@34.93.240.217:8082/manager/text/deploy?path=/hello&update=true"'
							//}
						    }
						}

	    	
					    stage("Jacoco Code Coverage report") {
						    steps {
							 jacoco(execPattern: 'target\\*.exec')
								}
						    } 
		     

				 stage('Release Approval') {
					 when {
					 branch 'master'
					 }
					steps {
						echo 'Confirm the deployment'
						echo "waiting for approval"
						timeout( time: 120, unit: "SECONDS" ){
						input 'Do you want to proceed to the Deployment?'
						milestone 1
									}
						}
					} 

					/*stage('Staging snapshot version') {
					    steps {
							echo 'set version to snapshot'
							script{
								def releasedVersion = getReleaseVersion()
						print releasedVersion
						sh "mvn versions:set -DgenerateBackupPoms=false -DnewVersion=${releasedVersion}"
					}
				    }
				}*/			
			
			stage('Sanity Test') {
			    steps {
				echo 'clean test'
				sh 'mvn clean test'

			    }
			}
        
        stage('Release Artifact Publish to Nexus') {
            steps {
                echo 'Upload artifact to nexus'
                script{
				def releasedVersion = getReleaseVersion()
                print releasedVersion
				withCredentials([usernamePassword(credentialsId: 'githubtoken', passwordVariable: 'passgit', usernameVariable: 'usergit')]) {
   				sh "mvn -B clean release:prepare release:perform -Darguments='-Dmaven.javadoc.skip=true' -Dusername=${usergit} -Dpassword=${passgit} -Dtag=release-${releasedVersion} -DreleaseVersion=${releasedVersion}"
					}
                }
			}
			
        }
        
        stage('Promote Artifact to UAT ') {
            steps {
                sh 'curl --upload-file target/addressbook.war "http://tomcat:password@10.62.125.9:8083/manager/text/deploy?path=/addressbook-release&update=true"'
                //sh 'curl --upload-file target/addressbook.war "http://tomcat:password@34.93.238.186:8081/manager/text/deploy?path=/addressbook&update=true"'
                //withCredentials([usernamePassword(credentialsId: 'nexusadmin', passwordVariable: 'pass', usernameVariable: 'user')]) {
                //    sh 'curl --upload-file target/hello-world-war-1.0.0-SNAPSHOT.war "http://${user}:${pass}@34.93.240.217:8082/manager/text/deploy?path=/hello&update=true"'
                //}
            }
        }
            
			
    }     
        
    tools {
        maven 'maven3.3.9'
        jdk 'openjdk8'
    }

     environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "10.62.125.9:8084"
        //NEXUS_URL = "35.200.184.59:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "maven-snapshots"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexusadmin"
    }   

    post {
         always {
            echo 'JENKINS PIPELINE'
            

        }
        success {
            echo 'JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo 'JENKINS PIPELINE FAILED'
        }
        unstable {
            echo 'JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo 'JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
    }

     
}
def getReleaseVersion() {
                        def pom = readMavenPom file: 'pom.xml'
                        print pom.version
                        def versionNumber;
                        versionNumber = env.BUILD_NUMBER;
                        print versionNumber
                        return pom.version.replace("-SNAPSHOT", ".${versionNumber}")
                    }                        
