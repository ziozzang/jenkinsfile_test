node('docker') {
  checkout scm
  stage('Check Environments') {
    sh 'pwd'
    sh 'df -h'
    sh 'ls -al'
  }
  stage('Docker Cleanup Phase') {
    sh 'docker rm -f base_run'
    sh 'docker rmi -f base'
  }
  stage('Build Test') {
    sh 'docker build -t base base'
  }
  stage('Image Test') {
    docker.image('base').inside {
      sh 'python --version'
    }
  }
}
