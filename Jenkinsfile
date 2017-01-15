#!groovy

node('general_ec2') {
    checkout scm

        String jdktool = tool name: 'jdk8', type: 'hudson.model.JDK'
        withEnv(["PATH+JDK=${jdktool}/bin", "JAVA_HOME=${jdktool}"]) {

            stage('Build & Deploy') {
                /* Call the Maven build without tests. */
                def server = Artifactory.server "artifactory"
                def buildInfo = Artifactory.newBuildInfo()
                buildInfo.env.capture = true
                def rtMaven = Artifactory.newMavenBuild()
                rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
                rtMaven.tool = 'maven339'
                rtMaven.run pom: 'pom.xml',
                        goals: 'install -U -s settings.xml',
                        buildInfo: buildInfo

                buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
                server.publishBuildInfo buildInfo
            }

        }
}