pipeline {
  agent {
    kubernetes {
      yaml '''
apiVersion: v1 
kind: Pod 
metadata: 
    name: dind 
spec: 
    containers: 
      - name: docker-cmds 
        image: docker:latest  
        env: 
          - name: DOCKER_HOST 
            value: tcp://localhost:2375 
      - name: dind-daemon 
        image: docker:dind 
        securityContext: 
            privileged: true 
        volumeMounts: 
          - name: docker-graph-storage 
            mountPath: /var/lib/docker 
    volumes: 
      - name: docker-graph-storage 
        emptyDir: {}  
''' 
    }
  }
  stages {
    stage('build') {
      steps {
           container('dind-daemon') {
                sh ' docker build -t catalog ./src/'

        }
      } 
    }
    stage('test') {
      steps {
         container('dind-daemon') {
                sh 'docker run -d -p 5000:5000 --name catalog-container catalog && docker exec catalog-container bash -c "python3 tests.py"'
         }
      }
  }
    stage('deploy') {
      steps {
        withKubeConfig([credentialsId:'kubeconfig', serverUrl:'https://learnk8scluster-fxdnjc5g.hcp.northeurope.azmk8s.io:443']){
          sh 'kubectl apply -f ./src/deployment.yaml'
        }
      }
    }
}
}
