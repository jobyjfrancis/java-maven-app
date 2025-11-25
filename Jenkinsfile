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
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
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
    } 
}
