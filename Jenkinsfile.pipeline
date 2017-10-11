/*
 * 작성: Jioh L. Jung<ziozzang@gmail.com>
 * 문법 가이드: https://jenkins.io/doc/book/pipeline/syntax/
 */
pipeline {
  // 트리거 조건을 명시
  /*
  triggers {
    cron('H 4/* 0 0 1-5')
  }
  */
  // 실행할 노드를 설정 합니다.
  agent {
    node {
      //노드 레이블 지정
      label 'docker'
      //만일 특정 위치에서 실행해야 한다면 워크스페이스 위치
      //customWorkspace '/some/other/path'
    }
  }
  // Global Variable
  environment {
    DISABLE_AUTH = 'true'
    DB_ENGINE    = 'sqlite'
    
    // This returns 0 or 1 depending on whether build number is even or odd
    FOO = "${currentBuild.getNumber() % 2}"
  }
  // 파라미터
  parameters {
    string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
  }
  // using the Timestamper plugin we can add timestamps to the console log
  options {
    timestamps()    
    //buildDiscarder(logRotator(numToKeepStr:'1'))
    //disableConcurrentBuilds()
    //skipDefaultCheckout(true)
    //timeout(time: 5, unit: 'MINUTES')
    
    // Only keep the 10 most recent builds
    //buildDiscarder(logRotator(numToKeepStr:'10'))
  }
  // 빌드 단계
  stages {
    stage("Always Skip") {
      when {
        // skip this stage unless the expression evaluates to 'true'
        expression {
          echo "Should I run?"
          return false
        }
      }
      steps {
        echo "World"
        writeFile file: "output/usefulfile.txt", text: "This file is useful, need to archive it."
      }
    }
    stage('Checkout') {
      when {
        // stage won't be skipped as long as FOO == 0, build number is even
        environment name: "FOO", value: "0"
      }
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']],
          userRemoteConfigs: [[url: 'http://git-server/user/repository.git']]])

        // create a directory called "tmp" and cd into that directory
        dir("tmp") {
          // use git to retrieve the plugin-pom
          git changelog: false, poll: false, url: 'git://github.com/jenkinsci/plugin-pom.git', branch: 'master'
          sh 'echo "M2_HOME: ${M2_HOME}"'
          sh 'echo "JAVA_HOME: ${JAVA_HOME}"'
          sh 'mvn clean verify -Dmaven.test.failure.ignore=true'
        }

      }
      post {
        success {
          // we only worry about archiving the jar file if the build steps are successful
          archiveArtifacts(artifacts: '**/target/*.jar', allowEmptyArchive: true)
        }
      }
    }
    stage('Test Envirnment') {
      steps {
        // 파라미터 출력 예제
        echo "Hello ${params.PERSON}"
        sh """
          docker build -t ${IMAGE} .
          docker tag ${IMAGE} ${IMAGE}:${VERSION}
          docker push ${IMAGE}:${VERSION}
        """
      }
    }
    stage('Build Docker') {
      /*
      agent {
        dockerfile {
          additionalBuildArgs '--build-arg foo=bar'
          dir 'someSubDir'
        }
      }
      */
      agent {
        docker {
          image 'maven:3-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }
      }
      steps {
      }
    }
    stage('Build Docker') {
      when {
        anyOf {
          branch 'master'; branch 'staging'
        }
        expression {
          BRANCH_NAME ==~ /(production|staging)/
          // currentBuild.getNumber() % 2 == 1
        }
        environment name: 'DEPLOY_TO', value: 'production'
      }
      
      //args "-v /tmp:/tmp -p 8000:8000"
      steps {
        writeFile text: 'hello world', file: 'msg.out'
        /*
         * Some steps may not be fully implemented with symbols or step names to run directly
         * This syntax can be used to call the class directly and run that step.
         * This syntax works fine in Declarative
         */
        step([$class: 'ArtifactArchiver', artifacts: 'msg.out', fingerprint: true])
      }
    }
    post {
      always {
        echo "Stage is done"
      }
    } 
    stage('Groovy Script Example') {
      steps {
        echo 'Hello World'
        script {
          // Basically Groovy Scripts
          def browsers = ['chrome', 'firefox']
          for (int i = 0; i < browsers.size(); ++i) {
            echo "Testing the ${browsers[i]} browser"
          }
          if (env.BRANCH_NAME == 'master') {
            echo 'I only execute on the master branch'
          }
          else {
            echo 'I execute elsewhere'
          }
          
          try {
            sh 'exit 1'
          }
          catch (exc) {
            echo 'Something failed, I should sound the klaxons!'
            throw
          }
        }
      }
    }
  }
  // Stages에 매칭이 됨
  post {
    always {
      echo 'One way or another, I have finished'
      deleteDir() /* clean up our workspace */
    }
    success {
      echo 'I succeeeded!'
    }
    unstable {
      echo 'I am unstable :/'
    }
    failure {
      echo 'I failed :('
      
      mail to: 'team@example.com',
        subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
        body: "Something is wrong with ${env.BUILD_URL}"
    }
    changed {
      echo 'Things were different before...'
    }
  }
  
  
  stages {
    stage("foo") {
      steps {
        // variable assignment (other than environment variables) can only be done in a script block
        // complex global variables (with properties or methods) can only be run in a script block
        // env variables can also be set within a script block
        script {
          foo = docker.image('ubuntu')
          env.bar = "${foo.imageName()}"
          echo "foo: ${foo.imageName()}"
        }
      }
    }
    stage('Sanity check') {
      steps {
        input "Does the staging environment look ok?"
      }
    }
    stage("bar") {
      when {
        // skip this stage unless branch is NOT master
        not {
          branch "master"
        }
      }
      environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
      }
      steps{
        echo "bar: ${env.bar}"
        echo "foo: ${foo.imageName()}"
      }
      steps{
        timeout(time: 5, unit: "SECONDS") {
          retry(5) {
            echo "hello"
          }
        }
      }
      steps {
        retry(3) {
          sh './flakey-deploy.sh'
        }
        timeout(time: 3, unit: 'MINUTES') {
          sh './health-check.sh'
        }
      }
    }
  }
  post {
    always {
      archive 'build/libs/**/*.jar'
      //archive includes: 'pkg/*.gem'
      junit 'build/reports/**/*.xml'
      
      // publish html
      publishHTML target: [
        allowMissing: false,
        alwaysLinkToLastBuild: false,
        keepAll: true,
        reportDir: 'coverage',
        reportFiles: 'index.html',
        reportName: 'RCov Report'
      ]
    }
  }
}
