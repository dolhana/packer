/**
 * This pipeline will run a Docker image build
 */

podTemplate(label: 'docker',
  containers: [containerTemplate(name: 'docker', image: 'docker:1.11', ttyEnabled: true, command: 'cat')],
  volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
  ) {

    def image = "packer"
    node('docker') {
        stage('Build') {
            git 'https://github.com/dolhana/packer.git'
            container('docker') {
                docker.withRegistry("https://278955949007.dkr.ecr.us-west-2.amazonaws.com", "ecr:us-west-2:jenkins") {
                    def image = docker.build(image, "docker")
                    image.push()
                    image.withRun("build packer/example.json")
                }
            }
        }
        stage('Publish') {
            container('docker') {
            }
        }
    }
}
