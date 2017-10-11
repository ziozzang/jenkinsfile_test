node('docker') {
  checkout scm
  stage('Phase1') {
    sh 'pwd'
    sh 'df -h'
    sh 'ls -al'
  }
/*  stage('Build') {
        docker.image('python:3.5.1').inside {
            sh 'python --version'
        }
    }
*/
}
