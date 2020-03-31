node {

    def mvnHome

    stage('Prepare') {
        //git 'git@github.com:3Prophet/landing.git'
        mvnHome = tool 'maven'
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean package"
    }

    stage('Image Build') {
        sh "${mvnHome}/bin/mvn docker:build"
    }

    stage ('Nexus Login') {
       withCredentials([
               string(credentialsId: 'nexus-login-docker', variable: 'NEXUS_LOGIN_DOCKER'),
               string(credentialsId: 'nexus-password-docker', variable: 'NEXUS_PASSWORD_DOCKER'),
               string(credentialsId: 'nexus-host-docker', variable: 'NEXUS_HOST_DOCKER'),
               string(credentialsId: 'nexus-port-docker', variable: 'NEXUS_PORT_DOCKER')]) {
           sh "docker login -u ${NEXUS_LOGIN_DOCKER} -p ${NEXUS_PASSWORD_DOCKER} ${NEXUS_HOST_DOCKER}:${NEXUS_PORT_DOCKER}"
       }
    }

    stage('Image Push') {
        sh "${mvnHome}/bin/mvn docker:push"
    }

    stage('Image Remove') {
        sh "${mvnHome}/bin/mvn docker:remove"
    }
}
