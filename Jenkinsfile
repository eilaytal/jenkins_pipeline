pipeline {
    agent { label '2' }

    stages {
        stage('Docker Build') {
            steps {
                script {
                    sh 'docker build -t eilaytal/weatherappsearchistory .'
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    sh 'docker run --name weatherapp-test --rm -p 5001:8000 -d eilaytal/weatherappsearchistory'
                    sh 'sleep 5' // Ensure the application is fully started
                    sh 'python3 testing.py'
                }
            }
        }

        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub', passwordVariable: 'HUB_PASSWORD', usernameVariable: 'HUB_USERNAME')]) {
                    script {
                        sh 'echo $HUB_PASSWORD | docker login -u $HUB_USERNAME --password-stdin'
                        sh 'docker tag eilaytal/weatherappsearchistory eilaytal/weatherappsearchistory:V1.${BUILD_NUMBER}' // Tagging with build number
                        sh 'docker push eilaytal/weatherappsearchistory:V1.${BUILD_NUMBER}' // Pushing to DockerHub with build number
                    }
                }
            }
        }

        stage('Clone and Update GIT') {
            steps {
                dir('git') {
                    script {
                        sh 'rm -rf gitops-manifests' // Clean up any existing repository
                        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            sh 'git clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/eilaytal/gitops-manifests.git'
                            sh 'git config user.email "your_email@example.com"' // Configures Git user email
                            sh 'git config user.name "Your Name"' // Configures Git username
                        }
                    }
                    dir('gitops-manifests/weatherapp_chart') {
                        script {
                            sh 'ls -la' // Debugging step to list files and verify location
                            sh """
                            sed -i 's/tag: .*/tag: "V1.${BUILD_NUMBER}"/' values.yaml // Updating image tag in values.yaml
                            """
                            sh 'cat values.yaml' // Outputs the contents of the file to verify the change
                            withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                                sh 'git add values.yaml'
                                sh 'git commit -m "Update deployment configuration to V1.${BUILD_NUMBER}"'
                                sh 'git push' // Pushing changes to GitHub
                            }
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                sh 'docker stop weatherapp-test || true'
                sh 'docker rm weatherapp-test || true'
            }
        }
        success {
            slackSend(channel: "#succeeded-build", color: "good", message: "Build #${env.BUILD_NUMBER} successful!") // Success notification
        }
        failure {
            slackSend(channel: "#devops-alerts", color: "danger", message: "Build #${env.BUILD_NUMBER} failed!") // Failure notification
        }
    }
}
