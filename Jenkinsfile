pipeline {
  environment {
    IMAGE_NAME='productpage'
    VERSION="v${env.BUILD_NUMBER}"
    REPO='evgenil'
    CREDENTIALS_ID='evgenil'
    SNYK_TOKEN="snyk"
  }
  agent any
  stages {
    stage('Build') {
      steps {
        script {
          sh'docker build --target build --tag ${IMAGE_NAME}:${VERSION}-build -f welcome/app/bookinfo/src/productpage/Dockerfile welcome/app/bookinfo/src/productpage/'
        }
      }
    }
    stage('Test') {
      steps {
        script {
          sh'docker build --target test --tag ${IMAGE_NAME}:${VERSION}-test -f welcome/app/bookinfo/src/productpage/Dockerfile welcome/app/bookinfo/src/productpage/'
          sh'docker run --rm ${IMAGE_NAME}:${VERSION}-test'
        }
      }
    }
    stage('Push') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/',"${CREDENTIALS_ID}") {
            sh'docker build --target build --tag ${REPO}/${IMAGE_NAME}:${VERSION} --tag ${REPO}/${IMAGE_NAME}:latest -f welcome/app/bookinfo/src/productpage/Dockerfile welcome/app/bookinfo/src/productpage/'
            sh'docker push ${REPO}/${IMAGE_NAME}:${VERSION}'
            sh'docker push ${REPO}/${IMAGE_NAME}:latest'
          }
        }
      }
    }
    stage('lint-test') {
      steps {
        script {
          sh 'docker build --target build --tag ${IMAGE_NAME}:${VERSION} -f welcome/app/bookinfo/src/ratings/Dockerfile welcome/app/bookinfo/src/ratings/'
        }
      }
    }
    stage('Security') {
      steps {
        script {
          sh 'docker build --target security --build-arg SNYK_TOKEN=${SNYK_TOKEN} --tag ${REPO}/${IMAGE_NAME}:${VERSION}-security -f welcome/app/bookinfo/src/reviews/Dockerfile welcome/app/bookinfo/src/reviews/'
          sh 'docker run --rm ${REPO}/${IMAGE_NAME}:${VERSION}-security'
        }
      }
    }
  }
}
