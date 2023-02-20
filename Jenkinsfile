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

        stage('trigger config change pipeline'){
            steps{
                script{
                    //sh "curl -v -k -user yogeshk04:1111063956391a5448953d7c62012b2113 -X POST -H ´cache-control: no-cache' -H ´content-type: application/x-www-form-urlencoded´ -data ´IMAGE_TAG=${IMAGE_TAG}´ ´http://3.125.153.159:8080/job/argocd_CD/buildWithParameters?token=argocd-config´"
                    sh "curl -v -k -u yogeshk04:1111063956391a5448953d7c62012b2113 -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' -d "IMAGE_TAG=${IMAGE_TAG}" 'http://3.125.153.159:8080/job/argocd_CD/buildWithParameters?token=argocd-config'"

                }
            }
        }
       
    }
}