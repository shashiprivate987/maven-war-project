pipeline {
  agent any
    stages {
    
        stage('git-checkout') {
            steps {
                //git 'https://github.com/vijay2181/maven-war-project1.git'
                git 'https://github.com/shashiprivate987/maven-war-project.git'
           }
        }
  
        stage('environment-setting') {
             steps {
                  script {
                       env.BUILD = "yes" // Setting env variable for Build 
                             }
                       }
                 }


        stage('Build')  {

            when {environment name: 'BUILD', value: 'yes'}
                steps { 
                    echo "Building artifacts ..."
                    sh "mvn clean package "
                    }
               }
        stage('Code Coverage') {

            when {environment name: 'BUILD', value: 'yes'}
                steps {
                    echo "Running Code Coverage ..." 
	   
	               }
                }
	
	   stage('SonarQube Analysis') {
            when {environment name: 'BUILD', value: 'yes'}
                steps{
                    echo 'code analysis'    //it will call static code analysis and capture the details
	               }
                }
	
  
        stage("Quality Gate") { 
            when {environment name: 'BUILD', value: 'yes'}
                steps{
	               echo 'quality gates'
                   }
                }
	
	
	   stage('Stage Artifacts') {          
  
            when {environment name: 'BUILD', value: 'yes'}
                steps {          
                    script { 
	               /* Define the Artifactory Server details */
                   //Artifactory.server is a fuction got by installing artifactory plugin
                    def server = Artifactory.server 'jfrog'                       
                    def uploadSpec = """{
                    "files": [{
                    "pattern": "target/project-3-1.0-SNAPSHOT.war",                                  
                    "target": "demoCICD"    
                                                       
            }]
        }"""                                                                                                //demoCICD is the repository name in jfrog
                           //if you want you can also give       "recursive": "false"       below target
         
        /* Upload the war to  Artifactory repo */
        server.upload(uploadSpec)
    }
   }
  }
	

 stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID
                )

                rtPublishBuildInfo (
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    serverId: SERVER_ID
                )
            }
        }
         stage ('Add interactive promotion') {
            steps {
                rtAddInteractivePromotion (
                    //Mandatory parameter
                    serverId: SERVER_ID,

                    //Optional parameters
                    targetRepo: 'result/',
                    displayName: 'Promote me please',
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER,
                    comment: 'this is the promotion comment',
                    sourceRepo: 'result/',
                    status: 'Released',
                    includeDependencies: true,
                    failFast: true,
                    copy: true
                )

                rtAddInteractivePromotion (
                    serverId: SERVER_ID,
                    buildName: JOB_NAME,
                    buildNumber: BUILD_NUMBER
                )
            }
         }
         stage ('Removing files') {
            steps {
                sh 'rm -rf $WORKSPACE/*'
            }
        }
         
        
  }
}
