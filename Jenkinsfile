node {

    def mvnHome

    def branch

    def pom

    def artifactId

    def artifactVersion

    stage('Prepare') {
        checkout scm
        mvnHome = tool 'maven'
        branch = "${env.BRANCH_NAME}"
        pom = readMavenPom file: 'pom.xml'
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

    if (branch == "master") {

        artifactId = pom.artifactId
        artifactVersion = pom.version.replase("-SNAPSHOT", "")
        jarFileName = "${artifactId}-${artifactVersion}.jar"

        // Example: ch/fastview/landing/1.1.3/landing-1.1.3.jar
        pathToArtifact = [pom.groupId.replaceAll("\\.", "/"), artifactId, artifactVersion, jarFileName].join("/")

        stage('Release') {
            sh "${mvnHome}/bin/mvn release:clean"
            sh "${mvnHome}/bin/mvn release:prepare"
            sh "${mvnHome}/bin/mvn release:perform"
        }

        stage('Download released artifact') {
            withCredentials([credentialsId: 'nexus-url', variable: 'NEXUS_URL']) {
                sh "curl -O ${NEXUS_URL}/repository/maven-releases/${pathToArtifact}"
            }
            sh "if [[ !(-a ./target) || !(-d ./target) ]]; then mkdir target; fi"
            sh "mv ./${jarFileName} ./target"
        }
    }

    if (branch == "develop" || barnch == "master" || branch ==~ /release.*/) {
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
}