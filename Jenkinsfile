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

        stage('Commit Version Update') {
          steps {
            script {
              withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                sh "git remote set-url origin https://${USER}:${PASS}@github.com:dhritisaluja/08-jenkins-java-maven-app.git"
                sh 'git add .'
                sh 'git commit -m "jenkins: version bump"'
                sh 'git push origin HEAD:jenkins-jobs'
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
