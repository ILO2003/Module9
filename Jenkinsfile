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
                    def dockerCmd = "docker run -p 8080:8080 -d ${IMAGE_NAME}"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@51.20.121.35 ${dockerCmd}"
                    }
                }
            }
        }               
    }
} 
