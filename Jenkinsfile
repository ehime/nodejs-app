pipeline {
  agent {
    docker {
      image 'ehime/build-container:latest'
    }
  }
  stages {
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }
    stage('Create Packer AMI') {
        steps {
          withCredentials([
            usernamePassword(credentialsId: 'ada90a34-30ef-47fb-8a7f-a97fe69ff93f', passwordVariable: 'AWS_SECRET', usernameVariable: 'AWS_KEY'),
            usernamePassword(credentialsId: '2facaea2-613b-4f34-9fb7-1dc2daf25c45', passwordVariable: 'REPO_PASS', usernameVariable: 'REPO_USER'),
          ]) {
            sh 'packer build -var aws_access_key=${AWS_KEY} -var aws_secret_key=${AWS_SECRET} packer/packer.json'
            sh 'git config user.name "ehime"'
            sh 'git config user.email dodomeki@gmail.com'
            sh 'git clone https://github.com/ehime/nodejs-app-terraform.git'
            sh '''
               cd nodejs-app-terraform
               terraform init
               terraform apply -auto-approve -var access_key=${AWS_KEY} -var secret_key=${AWS_SECRET}
               git add terraform.tfstate
               git commit -m "terraform state update from Jenkins"
               git push https://${REPO_USER}:${REPO_PASS}@github.com/ehime/nodejs-app-terraform.git master
            '''
        }
      }
    }
  }
  environment {
    npm_config_cache = 'npm-cache'
  }
}
