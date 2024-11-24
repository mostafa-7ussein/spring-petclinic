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


        // stage('Archive Artifacts') {
        //     steps {
        //         script {
        //             // Create artifact directory if it doesn't exist
        //             sh "mkdir -p ${ARTIFACT_DIR}"
        //             // Copy artifacts from the container to the host
        //             sh 'docker cp $(docker compose -f docker-compose.yaml -f docker-compose-dev.yaml ps -q app):/app/ ${ARTIFACT_DIR}'
        //         }
        //         archiveArtifacts artifacts: "${ARTIFACT_DIR}/app/*.jar", allowEmptyArchive: true
        //     }
        // }

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
    post {
        success {
            script {
                slackSend(channel: '#devops', color: '#00FF00', message: "Succeeded  ${env.JOB_NAME} - Build Number: ${env.BUILD_NUMBER} succeeded!")
            }
        }
        failure {
            script {
                slackSend(channel: '#devops', message: "Pipeline ${env.JOB_NAME} - Build Number: ${env.BUILD_NUMBER} failed!")
            }
        }
    }
}