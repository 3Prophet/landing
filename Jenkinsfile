node {

    def mvnHome

    stage('Prepare') {
        git 'git@github.com:3Prophet/landing.git'
        mvnHome = tool 'maven'
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean package"
    }
}
