pipeline {
    agent any
        environment {
        DOCKER_HUB_CREDENTIALS = 'dockerhublast'
        DOCKER_HUB_REPO = 'mostafahu/spring_petclinic'
        GITHUB_REPO = 'https://github.com/mostafa-7ussein/spring-petclinic.git'
        GITHUB_CREDENTIALS = 'githublast'
        BRANCH = 'main'
        RECIPIENT = 'toota353535@gmail.com'
        ARTIFACT_DIR = 'artifacts'
    }
      stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
    }