/*
 * 작성: Jioh L. Jung<ziozzang@gmail.com>
 * 문법 가이드: https://jenkins.io/doc/book/pipeline/syntax/
 */
pipeline {
  // 실행할 노드를 설정 합니다.
  agent {
    node {
      //노드 레이블 지정
      label 'docker'
    }
  }
  // using the Timestamper plugin we can add timestamps to the console log
  options {
    timestamps()
  }
  // 빌드 단계
  stages {
    stage('Clone Source') {
      steps {
        checkout([$class: 'GitSCM'])
        sh """
          ls -al
          df -h
        """
        sh 'who'
      }
    }
    stage('Sanity check') {
      steps {
        input "Does the staging environment look ok?"
      }
    }
  }
  // Stages에 매칭이 됨
  post {
    always {
      echo 'One way or another, I have finished'
      echo 'One way or another, I have finished'
      
      script {
        echo 'I execute elsewhere'
      }
    }
  }
  
}
