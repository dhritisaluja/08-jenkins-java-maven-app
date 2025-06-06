#!/usr/bin/env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
  [
    $class: 'GitSCMSource',
    remote: 'https://github.com/dhritisaluja/devops-bootcamp-jenkins-shared-library.git',
    credentialsId: 'github-credentials'
  ]
)

def gv
pipeline {   
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }

        stage("build jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }

        stage("build and push image") {
            steps {
                script {
                    buildImage('dhritisaluja/demo-app:jma-3.0')
                }
            }
        }
        
        stage("deploy") {
            steps {
                script {
                    gv.deployApp()
                }
            }
        }               
    }
}
