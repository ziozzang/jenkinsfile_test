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
        /*
        script {
          // For Gerrit
          // https://wiki.jenkins.io/display/JENKINS/Gerrit+Trigger#GerritTrigger-PipelineJobs
          git url: '<gerrit-git-repo-url>'
 
          // Fetch the changeset to a local branch using the build parameters provided to the
          // build by the Gerrit plugin...
          def changeBranch = "change-${GERRIT_CHANGE_NUMBER}-${GERRIT_PATCHSET_NUMBER}"
          sh "git fetch origin ${GERRIT_REFSPEC}:${changeBranch}"
          sh "git checkout ${changeBranch}"

        }
        */
        /*
        repo init -u git://gerrit.mycompany.net/mymanifest.git
        repo sync
        repo download $GERRIT_PROJECT $GERRIT_CHANGE_NUMBER/$GERRIT_PATCHSET_NUMBER
        */
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
