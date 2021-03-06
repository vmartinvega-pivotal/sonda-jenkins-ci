pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "echo 'TRACEABILITY: Build from sonda'"
        sh "mvn clean package"   
      }
    }
    stage('Build Docker Image') {
      environment {
        DOCKER_CREDS = credentials('dockerio')  // A jenkins credentials (username/password) is expected in jenkins (craete it before running)
      }
      steps {
        container('docker') {  
          sh "docker build -t vmartinvega/sonda-app:latest ."  // when we run in this step, we're running it via a shell on the docker build-pod container, 
          sh "docker login --username=$DOCKER_CREDS_USR --password=$DOCKER_CREDS_PSW"        // which is just connecting to the host docker deaemon
          sh "docker push vmartinvega/sonda-app:latest"        // which is just connecting to the host docker deaemon
        }
      }
    }
  }
}
