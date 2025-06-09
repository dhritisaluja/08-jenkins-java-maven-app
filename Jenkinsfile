pipeline {
    agent any
    tools{
        maven 'Maven'
    }
    environment {
        IMAGE_NAME = ''
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
                    def pomContent = readFile 'pom.xml'
                    def pom = new XmlSlurper().parseText(pomContent)
                    def newAppVersion = pom.version.text()                     
                    echo "New Application Version: ${newAppVersion}"

                    env.IMAGE_NAME = "${newAppVersion}-${env.BUILD_NUMBER}"
                    echo "Docker Image to be built: ${env.IMAGE_NAME}"
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
                    echo "Building Docker image: ${env.IMAGE_NAME}"
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t dhritisaluja/demo-app:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push dhritisaluja/demo-app:${IMAGE_NAME}"
                    }
                } // closing script
            } // closing steps
        } // closing stage

        stage('Deploy') {
            steps {
                echo "Deploy stage (to be implemented)..."
            }
        }
    } // closing stages
} // closing pipeline
