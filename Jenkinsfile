pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    
    environment {
        DOCKER_REGISTRY_USER = 'dhritisaluja'
        APP_NAME = 'demo-app'
        IMAGE_NAME_WITH_TAG = '' // Initialize as empty, will be set later
    }

    stages {
        stage('Increment Version') {
            steps {
                script {
                    echo "Incrementing application version..."
                    
                    sh '''
                       mvn build-helper:parse-version \\
                       versions:set \\
                       -DnewVersion=\\${parsedVersion.majorVersion}.\\${parsedVersion.minorVersion}.\\${parsedVersion.nextIncrementalVersion} \\
                       versions:commit
                    '''

                    echo "Reading new version from pom.xml..."
                    def newAppVersion = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                 
                    echo "New Application Version: ${newAppVersion}"

                    
                    def imageTag = "${newAppVersion}-${env.BUILD_NUMBER}"
                    env.IMAGE_NAME_WITH_TAG = "${env.DOCKER_REGISTRY_USER}/${env.APP_NAME}:${imageTag}"
                    
                    echo "Docker Image to be built: ${env.IMAGE_NAME_WITH_TAG}"
                }
            }
        }

        stage('Build App') {
            steps {
                echo "Building application with new version..."
                sh "mvn clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    
                    echo "Building Docker image: ${env.IMAGE_NAME_WITH_TAG}"
                    
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        
                        sh "docker build -t ${env.IMAGE_NAME_WITH_TAG} ."
                        
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        
                       
                        sh "docker push ${env.IMAGE_NAME_WITH_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploy stage (to be implemented)..."
            }
        }
    }
}
