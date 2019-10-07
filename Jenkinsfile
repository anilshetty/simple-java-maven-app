pipeline {
    agent any
    environment { 
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        ANSIBLE_HOSTS = "/etc/ansible/ec2.py"
        EC2_INI_PATH = "/etc/ansible/ec2.ini"

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
                
                sh "echo $ANSIBLE_HOSTS"
                sh "echo $EC2_INI_PATH"
                sh "ansible tag_Name_CMDC -i /etc/ansible/ec2.py -m ping"
                    
                
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
