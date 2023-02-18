pipeline {

    agent any

    environment{
        DOCKERHUB_USERNAME= "yogeshk04"
        APP_NAME= "argocd-py-app"
        IMAGE_TAG= "${BUILD_NUMBER}"
        IMAGE_NAME= "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS= 'dockerhub'
    }

    stages{
        stage('Cleanup WS'){

            steps{
                script{
                    cleanWs()
                }
            }
        }

        stage('Git Checkout'){

            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/yogeshk04/argocd-project.git',
                    branch: 'master'
                }
            }                
        }

        stage('Build Docker Image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }

        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('', REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete Docker images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Update k8s deployment file'){
            steps{
                script{
                    sh """
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                    cat deployment.yaml
                    """
                }
            }
        }
    }
}