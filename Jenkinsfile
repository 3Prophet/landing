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
        sh "${mvnHome}/bin/mvn dockerimage:build"
    }

    stage('Image Push') {
        sh "${mvnHome}/bin/mvn dockerimage:push"
    }

    stage('Image Remove') {
        sh "${mvnHome}/bin/mvn docker:remove"
    }
}
