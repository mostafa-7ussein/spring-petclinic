pipeline {
    agent any
        environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhublast'
        DOCKER_HUB_REPO = 'mostafahu/spring_petclinic'
        GITHUB_REPO = 'https://github.com/mostafa-7ussein/spring-petclinic.git'
        GITHUB_CREDENTIALS = 'githublast'
        BRANCH = 'main'
        ARTIFACT_DIR = 'artifacts'
    }
  stages {
        stage('Checkout') {
            steps {
                 script {
                     checkout([$class: 'GitSCM', branches: [[name: "${BRANCH}"]],
                         userRemoteConfigs: [[url: "${GITHUB_REPO}", credentialsId: "${GITHUB_CREDENTIALS}"]]
                     ])
                 }
            }
        }

        stage('Build') {
            steps {                
                script {
                    sh 'docker compose -f docker-compose.yaml -f docker-compose-dev.yaml build'
                }
            }
        }


        stage('Archive Artifacts') {
            steps {
                script {
                    // Create artifact directory if it doesn't exist
                    sh "mkdir -p ${ARTIFACT_DIR}"
                    // Copy artifacts from the container to the host
                    sh 'docker cp $(docker compose -f docker-compose.yaml -f docker-compose-dev.yaml ps -q app):/app/ ${ARTIFACT_DIR}'
                }
                archiveArtifacts artifacts: "${ARTIFACT_DIR}/app/*.jar", allowEmptyArchive: true
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    // Push the built images to Docker Hub
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh """
                        echo "${PASSWORD}" | docker login -u "${USERNAME}" --password-stdin
                        docker compose -f docker-compose.yaml -f docker-compose-dev.yaml push
                        docker logout
                        """
                    }
                }
            }
        }
    }

    // post {
    //     success {
    //         mail subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
    //                  body: "Good news! Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' succeeded.",
    //                  to: "${RECIPIENT}"
    //     }
    //     failure {
    //         mail subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
    //                  body: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' failed. Please check the logs for details.",
    //                  to: "${RECIPIENT}"
    //     }
    //     cleanup {
    //         script {
    //             // List and remove containers, networks, images, and volumes specific to the project
    //             sh 'docker compose -f spring-petclinic/docker-compose.yml -f spring-petclinic/docker-compose-dev.yml down'
    //         }
    //         // Clean up workspace and project-specific Docker containers, networks, images, and volumes
    //         cleanWs()
    //     }
    // }
    }