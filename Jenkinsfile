pipeline {
  agent none
  stages {
    stage('Checkout') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        git branch: 'main', url: 'https://github.com/dlawnsgh508-stack/test-cicd.git'
      }
    }
    stage('Test Application') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        sh 'mvn test'
      }
    }
    stage('Build Application') {
      agent {
        docker { image 'maven:3-eclipse-temurin-21' }
      }
      steps {
        sh 'mvn clean package -DskipTests=true'
      }
    }
    stage('Build Container Image') {
      agent { label 'controller' }
      steps {
        sh 'docker image build -t my-tomcat .'
      }
    }
    stage('Tag Container Image') {
      agent { label 'controller' }
      steps {
        sh "docker image tag my-tomcat limjunho/my-tomcat:${BUILD_NUMBER}"
        sh 'docker image tag my-tomcat limjunho/my-tomcat:latest'
      }
    }
    stage('Push Container Image') {
      agent { label 'controller' }
      steps {
        withDockerRegistry(credentialsId: 'docker-registry-credential', url: 'https://index.docker.io/v1/') {
          sh "docker image push limjunho/my-tomcat:${BUILD_NUMBER}"
          sh 'docker image push limjunho/my-tomcat:latest'
        }
      }
    }
    stage('Run Container') {
      agent { label 'controller' }
      steps {
        ansiblePlaybook(playbook: 'myweb-playbook.yaml')
      }
    }
  }
}
