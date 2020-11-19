podTemplate(
    serviceAccount: 'jenkins',
    containers: [
        containerTemplate(
          name: 'jnlp', 
          image: 'jenkins/inbound-agent:4.3-4',
          args: '${computer.jnlpmac} ${computer.name}'
        ),
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
        hostPathVolume(mountPath: '/bin/docker', hostPath: '/bin/docker'),
        hostPathVolume(mountPath: '/usr/bin/kubectl', hostPath: '/usr/bin/kubectl')
    ]
)

{
    node(POD_LABEL) {
        stage('git scm update') {
            git url: 'https://github.com/IaC-Source/echo-ip.git', branch: 'main'
        }
        stage('docker build and push') {
            '''sh
            docker build -t 192.168.1.10:8443/echo-ip:latest .
            docker push 192.168.1.10:8443/echo-ip:latest
            '''
        }
        stage('deploy kubernetes') {
            '''sh
            kubectl create deployment echo-ip --image=192.168.1.10:8443/echo-ip:latest
            kubectl expose deployment echo-ip --type=LoadBalancer --port=8080 --target-port=80
            '''
        }
    }
}
