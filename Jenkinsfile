pipeline {
    agent any

    environment {
        SONAR_SCANNER_HOME = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
        MAVEN_HOME = tool name: 'Maven', type: 'hudson.tasks.Maven$MavenInstallation'
        DOCKER_HUB_CREDENTIALS = credentials('7948c65a-93a3-42f1-ace7-17330ddeac97') // Configure this in Jenkins credentials
        DOCKER_IMAGE_NAME = 'testingkyaw/pet-clinic'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: 'main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: false, timeout: 10]], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/yourusername/your-repo.git']]])
            }
        }

        stage('Build') {
            steps {
                sh "${tool(name: 'Maven')}/bin/mvn clean install"
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withSonarQubeEnv('SonarCloud') {
                    sh "${tool name: 'SonarScanner'}/bin/sonar-scanner"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build(env.DOCKER_IMAGE_NAME, '-f .')

                    docker.withRegistry('https://registry.hub.docker.com', env.DOCKER_HUB_CREDENTIALS) {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
