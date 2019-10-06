pipeline {
    agent any
    stages {
        

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://localhost:8081/artifactory",
                    credentialsId: "rt_login"
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
                rtBuildInfo (
                    captureEnv: true
                )
            }
        }

        stage ('Build, Test and Package') {
            steps {
                rtMavenRun (
                    tool: 'maven', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean package',
                    opts: '-DbuildNumber="${env.BUILD_NUMBER}"',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER",
                    buildNumber: "${env.BUILD_NUMBER}"
                    
                )
            }
        }
        stage ('Download') {
            steps {
                input { message: "should we download?" }
                rtDownload (
                    
                    buildNumber: "${env.BUILD_NUMBER}",
                    serverId: 'ARTIFACTORY_SERVER',
                    spec: '''{ "files": [
            {
              "pattern": "libs-snapshot-local/com/mycompany/app/my-app/1.0-SNAPSHOT-${env.BUILD_NUMBER}/*.jar",
              "target": "/tmp/"
            }
         ]
    }'''
                )
            }
        }
    }
}
