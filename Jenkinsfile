pipeline {
  agent any

  tools {
    maven 'M2_HOME'
    
    }
  environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY') 
  } 
  
  stages {
   stage('CheckOut') {
      steps {
        echo 'Checkout the source code from GitHub'
        git branch: 'master', url: 'https://github.com/swethaenukonda/Banking-project.git'
            }
    }
    
    stage('Package the Application') {
      steps {
        echo " Packaging the Application"
        sh 'mvn clean package'
            }
    }
    
    stage('Publish Reports using HTML') {
      steps {
      publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '/var/lib/jenkins/workspace/Banking-project/target/surefire-reports', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
            }
    }
    
   stage('Docker Image Creation') {
      steps {
        sh 'docker build -t swethamba859/banking-project:1.0 .'
            }
    }
    stage('DockerLogin') {
      steps {
          withCredentials([usernamePassword(credentialsId: 'docker1hub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
         sh 'docker login -u ${docker_user} -p ${docker_password}'
            }
        }
    } 
  
    stage('Push Image to DockerHub') {
      steps {
        sh 'docker push swethamba859/banking-project:1.0'
            }
    } 
        stage ('Configure Test-server with Terraform, Ansible and then Deploying'){
            steps {
                dir('my-serverfiles'){
                sh 'sudo chmod 600 jenkinskey.pem'
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform apply --auto-approve'
                }
            }
        }
        /*stage ('Deploy into test-server using Ansible') {
           steps {
             ansiblePlaybook credentialsId: 'jenkinskey', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory', playbook: 'banking playbook.yml'
           }
               }*/
     }

        }
    



