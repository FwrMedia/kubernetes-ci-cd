node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName
    env.BUILD_TAG=tag
    def currentWorkingDirectory = pwd();
    stage("php_lint") {
        sh "find ${currentWorkingDirectory}/applications/hello-kenzan/test-php-files -name "*.php" -print0 | xargs -0 -n1 php -l"
     }
    stage ('Build') {
        echo "### Bulding docker image ###"
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    }
    
    stage ('Push') {
        echo "### Pushing docker image ###"
        sh "docker push ${imageName}"
     }

    stage ('Deploy') {
        echo "### Deploying docker image ###"
        kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'
     }

}