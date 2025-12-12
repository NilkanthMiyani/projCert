pipeline {
  agent {
    label 'slave1'
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
  }
  stages {
        stage('Puppet Agent Installation') {

        steps {
            sh '''sudo -i
                    wget https://apt.puppetlabs.com/puppet6-release-focal.deb
                    sudo dpkg -i puppet6-release-focal.deb
                    sudo apt-get update -y
                    sudo apt-get install puppet-agent -y'''
            }
        }   

        stage('SCM_Checkout') {
            steps {
            echo 'Perform SCM_Checkout'
            git 'https://github.com/nilkanthmiyani/projCert.git'
            }
        }

        stage ('Docker installation'){
    
          steps{
              git 'https://github.com/nilkanthmiyani/projCert.git'
              dir('.') {
                sh ' sudo ansible-playbook playbook.yaml -i inventory.txt'
              }
          }       
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker version'
                sh "docker build -t nmiyani/phpwebapp:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag nmiyani/phpwebapp:${BUILD_NUMBER} nmiyani/phpwebapp:latest"
                }
        }
        stage('Login2DockerHub') {

            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Publish_to_Docker_Registry and Running container"') {
        steps {
            sh "docker push nmiyani/phpwebapp:latest"
            sh "docker run -dit -p 8089:8080 nmiyani/phpwebapp"
            }
        }    
  }
}
