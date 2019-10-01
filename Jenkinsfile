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
            }
        }

        stage ('Build') {
            steps {
                rtMavenRun (
                    tool: 'maven', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean compile',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        stage ('Test') {
            steps {
                rtMavenRun (
                    tool: 'maven', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'test',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }
        stage ('Package') {
            steps {
                rtMavenRun (
                    tool: 'maven', // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'package',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
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
