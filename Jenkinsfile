// multistage
pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDS = credentials('DockerCredentials')
        stages {
            stage('Tooling veriosns') {
                steps {
                    sh '''
                      docker --version
                      docker compose version
                    '''
                }
            }
            stage('Build')
            {
                sh 'docker context use default'
                sh 'docker compose  build'
                sh 'docker compose push'
            }
        }
    }
}
