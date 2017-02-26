node {
    mvnHome = tool name: 'maven', type: 'maven'
    milestone 10
    stage('Continuous Integration') {
        checkout scm
        mvn 'versions:lock-snapshots'
        mvn '-U -P sign clean deploy'
        stash includes: '**/pom.xml', name: 'poms'
    }
}
milestone 20
input message: 'Release ?', ok: 'Release', submitter: 'ymenager'
milestone 30
node {
    mvnHome = tool name: 'maven', type: 'maven'
    stage('Release') {
        checkout scm
        unstash 'poms'
        def pom = readMavenPom file: 'pom.xml'
        def version = pom.version.replace("-SNAPSHOT", "")
        echo "Releasing version ${version}"
        mvn "versions:set -DnewVersion=${version}"
        mvn "-P sign -U clean deploy"
        junit '**/target/surefire-reports/TEST-*.xml'
        archive '**/target/*.jar'
    }
    milestone 40
}

def mvn(args) {
    withMaven( maven: 'maven', mavenSettingsConfig: 'maven-global-settings') {
        sh "mvn -Dmaven.test.failure.ignore -U ${args}"
    }
    junit '**/target/surefire-reports/TEST-*.xml'
}
