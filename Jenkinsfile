#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@main', retriever: modernSCM(
        [$class: 'GitSCMSource',
        remote: 'https://github.com/jobyjfrancis/jenkins-shared-library.git',
        credentialsId: 'github-credentials'])


pipeline {   
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage("set version") {
            steps {
                script {
                    echo "setting app version..."
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "jobyjfrancis/devops-bootcamp:jma-$version-$BUILD_NUMBER"
                }
            }
        }
        stage("build app") {
            steps {
                script {
                     buildJar()
                }
            }
        }

        stage("build and push image") {
            steps {
                script {
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
             }
        }

        stage("deploy") {
            environment {
                AWS_ACCESS_KEY_ID = credentials('aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('aws_secret_access_key')
                APP_NAME = 'java-maven-app'
            }

            steps {
                script {
                    echo "Deploying docker image..."
                    sh 'envsubst < kubernetes/deployment.yaml | kubectl apply -f -'
                    sh 'envsubst < kubernetes/service.yaml | kubectl apply -f -'
                }
            }
        }

    } 
}
