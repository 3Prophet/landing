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

}
