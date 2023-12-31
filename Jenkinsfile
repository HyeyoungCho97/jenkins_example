pipeline{
  agent any
  environment{
    DOCKERHUB = credentials("dockerhub")
    GITHUB_REPO = "https://github.com/HyeyoungCho97/jenkins_example"
    DOCKER_REPO = "hyeyoungcho97/jenkins_example"
  }
  stages{
    stage('Check Out'){
      steps{
        echo 'check out'
        git branch: 'main',
            credentialsId: 'github',
            url: "$GITHUB_REPO"
      }
    }
    stage('Docker Build'){
      steps{
        sh 'docker build -t hello_world .'
      }
    }
    stage('Docker Upload'){
      steps{
        echo "upload"

        sh 'docker login -u $DOCKERHUB_USR -p $DOCKERHUB_PSW'
        sh 'docker tag hello_world $DOCKER_REPO'
        sh 'docker push $DOCKER_REPO'
        sh 'docker logout'
      }
    }
     stage('SSH transfer') {
        steps([$class: 'BapSshPromotionPublisherPlugin']) {
            sshPublisher(
              continueOnError: false, failOnError: true,
                publishers: [
                    sshPublisherDesc(
                      configName: "remote_server",
                      verbose: true,
                      transfers: [
                        sshTransfer(execCommand: "docker pull $DOCKER_REPO"),
                        sshTransfer(execCommand: "docker ps -aq --filter 'name=hello_world_server' | xargs -r docker stop"),
                        sshTransfer(execCommand: "docker ps -aq --filter 'name=hello_world_server' | xargs -r docker rm"),
                        sshTransfer(execCommand: "docker run -d --name hello_world_server -p 8000:8000 $DOCKER_REPO")
//                            sshTransfer(sourceFiles: "helm/**",)
                      ]
                    )
                ]
            )
        }
    }
  }
  post {
    success{
      slackSend(
        channel: '#jenkins',
       message: "${env.BUILD_NUMBER} ${env.JOB_NAME}"
      )
    }
  }
}
