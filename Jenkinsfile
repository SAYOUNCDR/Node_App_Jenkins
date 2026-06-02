pipeline{
    agent any
    tools{
        nodejs "NODE22"
    }
    environment{
        DOCKER_IMAGE = "say0xoun/node-app-jenkins"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "node-app-jenkins-container"
        PORT = "3000"
    }
    triggers{
        pollSCM('H/2 * * * *')
    }
    stages{
        stage("clone repo"){
            steps{
                git url: "https://github.com/SAYOUNCDR/Node_App_Jenkins",
                branch: "main"
            }
        }
        stage("install deps"){
            steps{
                bat "npm install"
            }
        }
        stage("build docker image"){
            steps{
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        stage("docker login and push image"){
            steps{
                withCredentials([
                    usernamePassword(
                        credentialsId:"dockerhub",
                        usernameVariable:"DOCKER_USERNAME",
                        passwordVariable:"DOCKER_PASSWORD"
                    )
                ])
                {
                    bat """
                        echo %DOCKER_PASSWORD%| docker login -u %DOCKER_USERNAME% --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    """
                }
            }
        }

        stage("stop old container"){
            steps{
                bat "docker rm -f ${CONTAINER_NAME}"
            }
        }

        stage("run new container"){
            steps{
                bat "docker run -d -p ${PORT}:3000 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
    }

    post{
        success{
            echo "success"
        }
    }
}