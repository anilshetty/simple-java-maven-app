pipeline {
    agent any
    environment { 
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_KEY')]) {
    // some block
            AWSKEYID = "${AWS_KEY}"
}
    withCredentials([string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET')]) {
        AWSSECRET = "${AWS_SECRET}"
}
    }
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
                    serverId: "ARTIFACTORY_SERVER"
                    
                )
            }
        }
        stage ('Print') {
            steps {
                sh "echo $AWSKEYID"
                sh "echo $AWSSECRET"
                    
                
            }
        }
        stage ('Deploy') {
          steps {
              
              withCredentials([string(credentialsId: 'ansible-vault-pwd', variable: 'ansible_vault_pwd')]) {
    ansiblePlaybook(
                playbook: 'deploy.yml',
                colorized: true,
                vaultCredentialsId: 'ansible-vault-pwd'
    )
              }   
              
              
                          
            
          }
        }
    }
}
