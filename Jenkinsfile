node('docker') {
  // 소스 코드 체크아웃 합니다.
  checkout scm
  
  // 환경 체크를 진행 함.
  stage('Check Environments') {
    sh 'pwd'
    sh 'df -h'
    sh 'ls -al'
    // multiple lines
    sh '''
            echo "Multiline shell steps works too"
            ls -lah
        '''
  }

  // 도커 이미지를 클린업 합니다.
  stage('Docker Cleanup Phase') {
    //sh 'docker rm -f base_run || true'
    sh 'docker rmi -f base || true'
  }
  
  // 블록으로 감싸기
  try {
    // 도커 이미지를 만들어 보기
    stage('Build Test') {
      sh 'docker build -t base base'
    }
    
    // 만들어진 이미지를 테스트 함.
    stage('Image Test') {
      docker.image('base').inside {
        sh 'python --version'
        sh '/sbin/ifconfig'
        sh 'cat /etc/*release'
      }
      sh 'docker ps'
    }
    
    // 아티팩트 저장
    stage('Save Artifact') {
      //archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
      //junit 'build/reports/**/*.xml'
    }
  }
  catch (exc) {
    echo 'I failed'
  }
  finally {
    // 여기는 무조건 파이널라이즈
    sh 'docker rmi -f base || true'
    
    // 상태에 따라 동작 정의
    if (currentBuild.result == 'UNSTABLE') {
      echo 'I am unstable :/'
      
      stage('Notification') {
        // 메일 발송
        /*
        mail to: 'team@example.com',
             subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
        */
        // 슬랙 알림
        /*
        slackSend channel: '#ops-room',
                  color: 'good',
                  message: "The pipeline ${currentBuild.fullDisplayName} completed successfully."
        */
      }
    }
    else {
      echo 'One way or another, I have finished'
    }
  }
  
  //=============================================
  stage('Build') {
    echo 'Building'
  }
  stage('Test') {
    echo 'Testing'
  }
  stage('Sanity check') {
    input "Does the staging environment look ok?"
  }
  stage('Deploy') {
    echo 'Deploying'
  }
}
