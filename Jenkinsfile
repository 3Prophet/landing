node {

    def mvnHome

    def branch

    stage('Prepare') {
        checkout scm
        mvnHome = tool 'maven'
        branch = "${env.BRANCH_NAME}"
    }

}
