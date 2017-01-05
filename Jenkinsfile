#!groovy

node('general_ec2') {
    checkout scm

        String jdktool = tool name: 'jdk8', type: 'hudson.model.JDK'
        withEnv(["PATH+JDK=${jdktool}/bin", "JAVA_HOME=${jdktool}"]) {
            stage('Checkout') {
                filteredCheckout()
            }

            stage('Build & Deploy') {
                /* Call the Maven build without tests. */
                def server = Artifactory.server "artifactory"
                def buildInfo = Artifactory.newBuildInfo()
                buildInfo.env.capture = true
                def rtMaven = Artifactory.newMavenBuild()
                rtMaven.tool = 'maven339'
                rtMaven.run pom: 'pom.xml',
                        goals: '-U -s settings.xml ' +
                                '-Dmaven.repository.base.url=http://10.0.0.184:881/artifactory/ ' +
                                '-Dmaven.repository.repos.url=http://10.0.0.184:881/artifactory/repos/ ',
                        buildInfo: buildInfo
                rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'

                buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
                server.publishBuildInfo buildInfo
            }

            stage('Results') {
                archive includes: 'target/*.jar,target/*.war'
            }
        }

}

void filteredCheckout() {
    // deleteDir()
    checkout([
            $class           : 'GitSCM',
            userRemoteConfigs: [[
                                        url: 'git@github.com:sapienstech/bdms-BigFunctions-exe-helper.git', credentialsId: '881cc29e-722a-462d-9953-0c6851bd45e5']]])
}

return