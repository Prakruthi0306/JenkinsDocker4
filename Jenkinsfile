// multistage
pipeline {
    agent any

        stages {
            stage('Source') {
                steps {
                    git url: 'https://github.com/Prakruthi0306/JenkinsDocker3.git'
                }
            }
            stage('Build') {
                steps {
                    script {
                        def mvnHome = tool 'MAVEN_HOME'
                        bat "${mvnHome}\\bin\\mvn -B verify"
                    }
                }
            }
            stage('SonarQube Analysis') {
                steps {
                    script {
                        def mvnHome = tool 'MAVEN_HOME'
                        withSonarQubeEnv() {
                            bat "${mvnHome}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=mysql"
                        }
                    }
                }
            }
            
         
            stage('Packaging') {
                steps {
                    step([$class: 'ArtifactArchiver', artifacts: '**/target/*.jar', fingerprint: true])
                }
            }
            stage('Docker compose'){
                steps{
   
      step([$class: 'DockerComposeBuilder'])
                }
            }
            stage ("Artifactory Publish"){
                steps{
                    script{
                            def server = Artifactory.server 'Artifactory'
                            def rtMaven = Artifactory.newMavenBuild()
                            //rtMaven.resolver server: server, releaseRepo: 'JenkinsDocker', snapshotRepo: 'Jenkinssnapshot'
                            rtMaven.deployer server: server, releaseRepo: 'JenkinsDocker', snapshotRepo: 'Jenkinssnapshot'
                            rtMaven.tool = 'MAVEN_HOME'
                            
                            def buildInfo = rtMaven.run pom: '$workspace/pom.xml', goals: 'clean install'
                            rtMaven.deployer.deployArtifacts = true
                            rtMaven.deployer.deployArtifacts buildInfo

                            server.publishBuildInfo buildInfo
                    }
                }
        }
        }
}
