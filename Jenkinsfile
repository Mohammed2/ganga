pipeline {
  agent any

  stages {
    // Build stage
    stage('Build') {
      steps {
        sh "docker build -t gangatest:${env.BRANCH_NAME} -f ${env.WORKSPACE}/python/Ganga/test/Dockerfile ."
      }
    }
    // Parallel testing stage
    stage('Test') {
      parallel {
        stage('GangaCore') {
          steps {
            sh "docker run --name GangaCore${env.BRANCH_NAME} gangatest:${env.BRANCH_NAME} /root/ganga/python/Ganga/test/Unit --cov-report xml --cov . --junitxml tests-GangaCore.xml || true"
            sh "docker cp GangaCore${env.BRANCH_NAME}:/root/tests-GangaCore.xml ${env.WORKSPACE}"
//          sh "docker cp GangaCore${env.BRANCH_NAME}:/root/coverage.xml ${env.$WORKSPACE}"
          }
          post {
            always {
              sh "docker rm GangaCore${env.BRANCH_NAME}" 
            }
          }
        }
        stage('GangaDirac') {
          steps {
            sh "docker build -t gangadiractest:${env.BRANCH_NAME} -f ${env.WORKSPACE}/python/GangaDirac/test/Dockerfile ."
            sh "docker run --name GangaDirac${env.BRANCH_NAME} -v ~/.globus:/root/.globus -e vo=gridpp gangadiractest:${env.BRANCH_NAME} /root/ganga/python/GangaDirac/test/Unit --cov-report xml --cov . --junitxml tests-GangaDirac.xml || true"
            sh "docker cp GangaDirac${env.BRANCH_NAME}:/root/tests-GangaDirac.xml ${env.WORKSPACE}"
          }
          post {
            always {
              sh "docker rm GangaDirac${env.BRANCH_NAME}"
              sh "docker rmi gangadiractest:${env.BRANCH_NAME}"
            }
          }
        }
        stage('GangaLHCb') {
          steps {
            sh "docker build -t gangalhcbtest:${env.BRANCH_NAME} -f ${env.WORKSPACE}/python/GangaLHCb/test/Dockerfile ."
            sh "docker run --privileged --name GangaLHCb${env.BRANCH_NAME} -v ~/.globus:/root/.globus gangalhcbtest:${env.BRANCH_NAME} || true"
            sh "docker cp GangaLHCb${env.BRANCH_NAME}:/root/tests-GangaLHCb.xml ${env.WORKSPACE}"
          }
          post {
            always {
              sh "docker rm GangaLHCb${env.BRANCH_NAME}"
              sh "docker rmi gangalhcbtest:${env.BRANCH_NAME}"
            }
          }
        }
      } // end parallel
    }
  }
  post { 
    always { 
      junit "**/tests-*.xml"
      sh "docker rmi gangatest:${env.BRANCH_NAME}"
    }
  }
}
