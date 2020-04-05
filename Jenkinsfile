node {

    def mvnHome

    def branch

    stage('Prepare') {
        checkout scm
        mvnHome = tool 'maven'
        branch = "${env.BRANCH_NAME}"
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean package"
    }
    stage('Unit Tests') {
        junit "**/target/surefire-reports/TEST-*.xml"
        archive "target/*.jar"
    }

    stage('Integration Tests') {
        sh "${mvnHome}/bin/mvn clean verify"
    }

    stage('Static code analysis') {
        sh "${mvnHome}/bin/mvn sonar:sonar"
    }

    if (branch == "master" || branch == "develop" || branch ==~ /release.*/) {
        stage('Nexus Login') {
            withCredentials([
                    string(credentialsId: 'nexus-login-docker', variable: 'NEXUS_LOGIN_DOCKER'),
                    string(credentialsId: 'nexus-password-docker', variable: 'NEXUS_PASSWORD_DOCKER'),
                    string(credentialsId: 'nexus-host-docker', variable: 'NEXUS_HOST_DOCKER'),
                    string(credentialsId: 'nexus-port-docker', variable: 'NEXUS_PORT_DOCKER')]) {
                sh "docker login -u ${NEXUS_LOGIN_DOCKER} -p ${NEXUS_PASSWORD_DOCKER} ${NEXUS_HOST_DOCKER}:${NEXUS_PORT_DOCKER}"
            }
        }
    }

    if (branch == "develop" || branch ==~ /release.*/) {
        stage('Image Build') {
            sh "${mvnHome}/bin/mvn docker:build"
        }
        stage('Image Push') {
            sh "${mvnHome}/bin/mvn docker:push -P develop"
        }
        stage('Image Remove') {
            sh "${mvnHome}/bin/mvn docker:remove"
        }
    }

    if (branch == "master") {
        stage('Release Image Build and Image Push') {
            sh "${mvnHome}/bin/mvn release:clean"
            sh "${mvnHome}/bin/mvn release:prepare"
            sh "${mvnHome}/bin/mvn release:perform"
            sh "${mvnHome}/bin/mvn docker:build"
            sh "${mvnHome}/bin/mvn docker:push -P develop"
            sh "${mvnHome}/bin/mvn docker:remove"
        }
    }
}