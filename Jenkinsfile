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
                   sh 'gradle --stacktrace --warning-mode all build' //Running our first build
                }
                
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://159.65.145:8081/artifactory",
                    credentialsId: 'admin.jfrog'
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

        stage ('Exec Gradle') {
            steps {
                rtGradleRun (
                    tool: "gradle6.8.2", // Tool name from Jenkins configuration
                    rootDir: "https://github.com/Ankitp110/GradleProject-TETRANOODLE/blob/master/",
                    buildFile: 'build.gradle',
                    tasks: 'clean artifactoryPublish',
                    deployerId: "GRADLE_DEPLOYER",
                    resolverId: "GRADLE_RESOLVER"
                )
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