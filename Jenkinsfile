#!/usr/bin.env groovy
library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/ILO2003/Module9-SL.git',
    credentialsID: 'github-credentials'
    ]
)

pipeline {   
    agent any
    tools{
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME = 'ilo2003/demo-app:java-maven-1.0'
    }
        stages {
            stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
            stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        } 
             stage("deploy") {
            steps {
                script {
                    echo "Deploying the application..."
                    
                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"

                    sshagent(['ec2-server-key']) {
                        sh "scp server-cmds.sh ec2-user@51.20.121.35:/home/ec2-user"
                        sh "scp docker-compose.yaml ec2-user@51.20.121.35:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@51.20.121.35 ${shellCmd}"
                    }
                }
            }
        }               
    }
} 
