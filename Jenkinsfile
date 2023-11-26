def registry = 'https://mfkimbell.jfrog.io/'

pipeline 
{
    agent 
	{
        node 
		{
            label 'maven'
        }
    }
environment 
{
    PATH = "/opt/apache-maven-3.9.5/bin:$PATH"
}
    stages 
	{
        stage("build")
		{
            steps 
			{
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true' //skip the unit tests on build
                 echo "----------- build complted ----------"
            }
        }
		stage("test"){
            steps{
                echo "----------- unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- unit test Complted ----------"
            }
        }

		// stage('SonarQube analysis') 
		// {
		// 	environment 
		// 	{
		// 		scannerHome = tool 'mfkimbell-sonar-scanner';
		// 	}
		// 	steps
		// 	{
		// 		withSonarQubeEnv('mfkimbell-sonarqube-server') 
		// 		{ 
		// 			sh "${scannerHome}/bin/sonar-scanner"	
		// 		}
			
        //     }
        // }

		// stage("Quality Gate")
		// {
		// 	steps 
		// 	{
		// 		script 
		// 		{
		// 			timeout(time: 1, unit: 'HOURS') 
		// 			{ // Just in case something goes wrong, pipeline will be killed after a timeout
		// 				def qg = waitForQualityGate() 
		// 				if (qg.status != 'OK') 
		// 				{
		// 					error "Pipeline aborted due to quality gate failure: ${qg.status}"
		// 				}
		// 			}
		// 		}
		// 	}
		// }


	
		stage("Jar Publish") 
		{
			steps 
			{
				script 
				{
						echo '<--------------- Jar Publish Started --------------->'
						def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
						def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
						def uploadSpec = """
						{
							"files": [
								{
								"pattern": "jarstaging/(*)",
								"target": "libs-release-local/{1}",
								"flat": "false",
								"props" : "${properties}",
								"exclusions": [ "*.sha1", "*.md5"]
								}
							]
						}"""
						def buildInfo = server.upload(uploadSpec)
						buildInfo.env.collect()
						server.publishBuildInfo(buildInfo)
						echo '<--------------- Jar Publish Ended --------------->'  
				
				}
			}   
		}   
	}
}

	

