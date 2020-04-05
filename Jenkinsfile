
node {
    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
    def server = Artifactory.server "deploy"
    // Create an Artifactory Maven instance.
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo
  
    
 rtMaven.tool = "maven"

    stage('Clone sources') {
        git url: 'https://github.com/djcuse/webapp.git'
    }

    stage('Artifactory configuration') {
        // Tool name from Jenkins configuration
        rtMaven.tool = "maven"
        // Set Artifactory repositories for dependencies resolution and artifacts deployment.
        rtMaven.deployer releaseRepo:'deploy', snapshotRepo:'deploy-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'deploy', snapshotRepo:'deploy-snapshot', server: server
    }

    stage('Maven build') {
        buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean install'
    }
   
    stage('Publish build info') {
        server.publishBuildInfo buildInfo
    }
	
stage ('BlazeMeter test'){
    sh "curl https://a.blazemeter.com/api/v4/user --user 'a6f2c324779ef94e812483e0:b1204ff7ab3aca3b7626a5cda670dfb1abeb4462c96fa6ee5eb39d6c1b467cdb05354052'"
  }
	
 stage ('Test') {	
	 script{
	 sh 'mvn -B -f functionaltest/pom.xml test'
	 }
	//rtMaven.run -B -f 'functionaltest/pom.xml', goals: 'test'

	 archive (includes: 'pkg/*.gem')
publishHTML (target: [
      allowMissing: false,
      alwaysLinkToLastBuild: false,
      keepAll: true,
      reportDir: '/functionaltest/target/surefire-reports',
      reportFiles: 'index.html',
      reportName: "RCov Report"
    ])
        }	
	
    stage ('Slack') {
        // send build started notifications
       // slackSend (color: '#FFFF00')
	  slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")  
      }

	
	
	
	
	
	
	
    }
	 
