node('docker') {
  checkout scm
  stage('Check Environments') {
    sh 'pwd'
    sh 'df -h'
    sh 'ls -al'
  }
  stage('Docker Cleanup Phase') {
    // Clean up docker
    //sh 'docker rm -f base_run || true'
    sh 'docker rmi -f base || true'
  }
  stage('Build Test') {
    sh 'docker build -t base base'
  }
  stage('Image Test') {
    docker.image('base').inside {
      sh 'python --version'
      sh '/sbin/ifconfig'
      sh 'cat /etc/*release'
    }
  }
}
