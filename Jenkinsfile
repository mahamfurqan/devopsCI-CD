pipeline {
    agent any 
    stages {
        stage('Checkout SCM') {
            steps {  
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/mahamfurqan/devopsCI-CD.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = "furqanmaham9308/app:${env.BUILD_NUMBER}"
                    bat "docker build -t ${imageName} ."
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script {
                    def imageName = "furqanmaham9308/app:${env.BUILD_NUMBER}"
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                        bat "docker login -u %DOCKERHUB_USER% -p %DOCKERHUB_PASS% docker.io"
                    }
                    bat "docker push ${imageName}"
                    bat "docker logout"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Update the image in the deployment YAML file
                    def imageName = "furqanmaham9308/app:${env.BUILD_NUMBER}"
                    def deploymentFile = 'deployment.yaml'

                    def deploymentContent = readFile(deploymentFile)
                    def updatedContent = deploymentContent.replaceAll('furqanmaham9308/app:latest', imageName)
                    writeFile file: deploymentFile, text: updatedContent

                    // Apply the deployment
                    withCredentials([file(credentialsId: 'kube', variable: 'KUBECONFIG')]) {
                        bat 'kubectl apply -f ./deployment.yaml'
                    }
                }
            }
        }
    }
}

