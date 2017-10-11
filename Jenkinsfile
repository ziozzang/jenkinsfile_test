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

        // 주: Nested stage는 실행되지 않음. 아래 stage는 무시 됨
        stage('Docker Inner Test') {
          sh 'python --version'
        }
      }
      sh 'docker ps'
    }
    // 아래처럼 사용 할수도 있음.
    docker.image('base').inside {
      stage('Docker Inner #1') {
        sh 'python --version'
      }
      stage('Docker Inner #2') {
        sh 'python --version'
      }
    }
    
    // 아티팩트 저장
    stage('Save Artifact') {
      //archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
      //junit 'build/reports/**/*.xml'
    }
  }
  catch (exc) {
    echo 'I failed'
    throw exc
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
    
    // 현재와 지난번의 빌드 결과를 테스트.
    def currentResult = currentBuild.result ?: 'SUCCESS'
    if (currentResult == 'UNSTABLE') {
      echo 'This will run only if the run was marked as unstable'
    }

    def previousResult = currentBuild.previousBuild?.result
    if (previousResult != null && previousResult != currentResult) {
      echo 'This will run only if the state of the Pipeline has changed'
      echo 'For example, if the Pipeline was previously failing but is now successful'
    }

    echo 'This will always run'
    
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
    
    // Retry & timeout 설정
    /*
    retry(3) {
      sh './flakey-deploy.sh'
    }

    timeout(time: 3, unit: 'MINUTES') {
      sh './health-check.sh'
    }
    
    timeout(time: 3, unit: 'MINUTES') {
      retry(5) {
        sh './flakey-deploy.sh'
      }
    }
    */
  }
}
