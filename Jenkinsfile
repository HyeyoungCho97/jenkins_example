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
