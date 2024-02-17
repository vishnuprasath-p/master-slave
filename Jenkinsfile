pipeline {
    agent { label 'Slave' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
	        APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "vishnu010194"
            DOCKER_PASS = 'Dockerci'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/vishnuprasath-p/master-slave'
                }
        }
        stage("Build Application"){
                steps {
                sh "mvn clean package"
            }

       }
        stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
       stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

       }
       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
    }

}
}