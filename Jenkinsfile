pipeline {
    agent any

    tools {
        maven 'Maven'
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
                    def version = sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
                    env.IMAGE_NAME = "$version-$BUILD_NUMBER"
                
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
    
                    echo "Building Docker image..."
                    
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t dhritisaluja/demo-app:${env.IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push dhritisaluja/demo-app:${env.IMAGE_NAME}"
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
