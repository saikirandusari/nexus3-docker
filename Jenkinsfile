node('gradle') {
    def serviceName="nexus3"
    def version="latest"
    def volumeCapacity="10Gi"

    stage('Checkout') {
        git url: "https://gitlab.com/arnaud-deprez/nexus-scripting.git"
    }
    stage('Build Nexus init scripts') {
        sh "gradle -Pci=true clean transformScriptToJson"
    }
    stage('Build Image') {
        // create a build dir context
        sh "rm -rf oc-build && mkdir -p oc-build"
        sh "cp -R Dockerfile build docker oc-build/"
        //create and start build
//        sh "oc import-image nexus3:${version} --from=docker.io/sonatype/nexus3:${version} --confirm"
//        sh "oc label is nexus3-sonatype build=nexus3 app=${serviceName}"
        sh "oc process -f openshift/nexus3-persistent-template.yml -p SERVICE_NAME=${serviceName} -p NEXUS_VERSION=${version} -p VOLUME_CAPACITY=${volumeCapacity} | oc apply -f -"
        sh "oc start-build ${serviceName}-docker --from-dir=oc-build"
        openshiftVerifyBuild bldCfg: "${serviceName}-docker", waitTime: "20", waitUnit: "min"
    }
    stage('Deploy') {
        sh "oc rollout latest dc/${serviceName}"
        openshiftVerifyDeployment depCfg: serviceName
    }
}