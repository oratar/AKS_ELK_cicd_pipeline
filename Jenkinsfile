podTemplate(yaml: '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: docker
            image: docker:latest
            command:
            - cat
            tty: true
            volumeMounts:
             - mountPath: /var/run/docker.sock
               name: docker-sock
          volumes:
          - name: docker-sock
            hostPath:
              path: /var/run/docker.sock    
''') {  

node('jenkins-slave') {
        stage('build docker image') {
                docker.build("catalog:latest","./src/)
        }
}
}
