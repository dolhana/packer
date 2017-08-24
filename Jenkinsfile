/**
 * This pipeline will run a Docker image build
 */

def dockerRegistryDomain = "278955949007.dkr.ecr.us-west-2.amazonaws.com"
def packerImageName = "packer"

podTemplate(label: 'docker',
  containers: [containerTemplate(name: 'docker', image: 'docker:17', ttyEnabled: true, command: 'cat')],
  volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
  ) {

    node('docker') {
        stage('Build Packer') {
            checkout scm
            container('docker') {
                docker.withRegistry("https://${dockerRegistryDomain}", 'ecr:us-west-2:jenkins') {
                    sh "docker build -t ${dockerRegistryDomain}/${packerImageName} docker"
                    docker.image("${dockerRegistryDomain}/${packerImageName}").push()
                }
            }
        }
    }
}

def awsAccessKey
def awsSecretKey

node {
    withCredentials(
        bindings: [[$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: "jenkins"]]) {
        awsAccessKey = env.AWS_ACCESS_KEY_ID
        awsSecretKey = env.AWS_SECRET_ACCESS_KEY
    }
}

podTemplate(label: 'packer',
            containers: [
                containerTemplate(
                    name: 'packer',
                    image: "${dockerRegistryDomain}/${packerImageName}",
                    envVars: [
                        containerEnvVar(key: 'AWS_ACCESS_KEY_ID', value: "${awsAccessKey}"),
                        containerEnvVar(key: 'AWS_SECRET_ACCESS_KEY', value: "${awsSecretKey}"),
                    ],
                    ttyEnabled: true,
                    command: 'cat')]) {
    node("packer") {
        stage("Build AMI") {
            checkout scm
            container("packer") {
                sh "env"
                dir(path: "packer") {
                    sh "/bin/packer build --debug --color=false example.json"
                }
            }
        }
    }
}

// Local Variables:
// mode: groovy
// End:
