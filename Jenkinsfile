pipeline {
  agent any
    options {
        skipDefaultCheckout(true)
    }
      parameters {
            string(name: 'server_ip', defaultValue: '')
        }
  environment {
    registry = "hossamalsankary/nodejs_app"
    registryCredential = 'docker_credentials'
    ANSIBLE_PRIVATE_KEY = credentials('secritfile')
  }

  stages {
   // install dependencies
    // stage("install dependencies") {

    //   steps {
    //     sh 'npm install'
    //   }
    //   post{
    //       always{
    //                sh 'bash ./clearDockerImages.sh'
    //       }
       
    //   }

    // }
    // stage("Test") {

    //   steps {

    //     sh 'npm run  test:unit'

    //   }

    // }

    // stage("Build") {

    //   steps {

    //     sh 'npm run build'
    //   }

    // }
    // stage("Build Docker Image") {
    //   steps {

    //     script {
    //       dockerImage = docker.build registry + ":$BUILD_NUMBER"
    //     }
    //   }
    //   post{
        
    //       failure{
    //         sh '  docker system prune --volumes -a -f '
    //       }
    //   }
    // }

    // stage("push image to docker hup") {
    //   steps {
    //     script {
    //       docker.withRegistry('', registryCredential) {
    //         dockerImage.push()
    //       }
    //     }
    //   }
    // }

    // stage("Test Docker Image In Dev Server ") {
    //   steps {
    //     sh ' docker run --name test_$BUILD_NUMBER -d -p 5000:8080 $registry:$BUILD_NUMBER '
    //     sh 'sleep 2'
    //     sh 'curl localhost:5000'
    //   }
  

    // }
    stage("Deply IAC ") {
      when {
          branch 'master'
      }
      steps {
        withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          dir("terraform-aws-instance") {
            // sh 'terraform init'
            // sh 'terraform destroy --auto-approve'
            // sh 'terraform apply --auto-approve'
            sh 'export IP =  $(terraform output  -raw server_ip)'
           
              
          }
        }
        script{

         params.server_ip = $ip
        }
        
      }
      post {

        success {
          echo "we  successful deploy IAC"
        }
        failure {
          withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {

            dir("terraform-aws-instance") {
              sh 'terraform destroy --auto-approve'

            }
          }
        }
      }
    }
    // stage("ansbile") {
    //   when {
    //       branch 'master'
    //   }
    //   steps {
    //     dir("./terraform-aws-instance") {
    //       withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {

    //         sh 'ansible-playbook -i ansbile/inventory/inventory --extra-vars ansible_ssh_host=$(terraform output  -raw server_ip) --extra-vars  IMAGE_NAME=$registry:$BUILD_NUMBER --private-key=$ANSIBLE_PRIVATE_KEY ./ansbile/inventory/deploy.yml '

    //       }
    //     }
    //   }
    // }
    stage("echo Ip"){
        steps{
          echo "${params.custom_var}"
        }
    }

  }

  post {
    always {
      cleanWs(cleanWhenNotBuilt: false,
              deleteDirs: true,
              disableDeferredWipeout: true,
              notFailBuild: true,
              patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                          [pattern: '.propsfile', type: 'EXCLUDE']])
        }
    success {
      echo "========A executed successfully========"
      sh 'bash ./clearDockerImages.sh'

    }
    failure {
           sh 'bash ./clearDockerImages.sh'
    }
  }
}