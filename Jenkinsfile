//START-OF-SCRIPT
pipeline {


    agent any

    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/Ankitp110/Java-servlet-web-application---CloudAcademy.git"
            }
        }

        stage('build') {
            
               tools {
               gradle 'gradle6.8.2'
              }      

            steps {
                script {
                   sh 'ls -la'
                   sh 'gradle clean build' //Running our first build
                }
                
            }
        } 

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://159.65.145.173:8081/artifactory",
                    credentialsId: 'bf4d6d00-015e-40de-8307-f50fd328760c'
                )

                rtGradleDeployer (
                    id: "GRADLE_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    repo: "libs-release-local",
                )

                rtGradleResolver (
                    id: "GRADLE_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    repo: "jcenter"
                )
            }
        }
        
        /* stage ('Config Build Info') {
            steps {
                rtBuildInfo (
                    captureEnv: true,
                    includeEnvPatterns: ["*"],
                    excludeEnvPatterns: ["DONT_COLLECT*"]
                )
            }
        }  */

        

        stage ('Gradle publish') {
            steps {
                sh 'pwd'
                rtGradleRun (
                    tool: "gradle6.8.2", // Tool name from Jenkins configuration
                    //rootDir: new URL('https://github.com/Ankitp110/Java-servlet-web-application---CloudAcademy/blob/master/build.gradle').file,
                    rootDir: '/var/jenkins_home/workspace/PipeLine_Cloudacapro',
                    buildFile: 'build.gradle',
                    tasks: 'clean --scan build ', //  artifactoryPublish
                    deployerId: "GRADLE_DEPLOYER",
                    resolverId: "GRADLE_RESOLVER"
                )
                sh 'ls -la'
                sh 'curl -u admin:Panju@12345 -X PUT "http://159.65.145.173:8081/artifactory" -T /var/jenkins_home/workspace/PipeLine_Cloudacapro/build/libs'
            }
        }
        
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
    }
}