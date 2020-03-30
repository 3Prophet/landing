node {

    def mvnHome

    stage('Prepare') {
        git 'git@github.com:3Prophet/landing.git'
        mvnHome = tool 'maven'
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean package"
    }

    stage('Image Build') {
        sh "${mvnHome}/bin/mvn docker:build"
    }

    stage ('Nexus Login') {
        environment {
            NEXUS_LOGIN_DOCKER = credentials('nexus-login-docker')
            NEXUS_PASSWORD_DOCKER = credentials('nexus-password-docker')
            NEXUS_HOST_DOCKER = credentials('nexus-host-docker')
            NEXUS_PORT_DOCKER = credentials('nexus-port-docker')
        }
        sh "docker login -u ${NEXUS_LOGIN_DOCKER} -p ${NEXUS_PASSWORD_DOCKER} ${NEXUS_HOST_DOCKER}:${NEXUS_PORT_DOCKER}"
    }

    stage('Image Push') {
        sh "${mvnHome}/bin/mvn docker:push"
    }

    stage('Image Remove') {
        sh "${mvnHome}/bin/mvn docker:remove"
    }
}
