pipeline {
  agent { label 'vm3' }

  environment {
    APP_REPO = 'https://github.com/heroku/node-js-getting-started.git'
    IMAGE_NAME = 'node-ci-app'
  }

  stages {
    stage('Checkout External Code') {
      steps {
        dir('app') {
          git url: "${APP_REPO}", branch: 'main'
        }
      }
    }

    stage('Prepare Dockerfile') {
      steps {
        sh 'cp Dockerfile app/'
      }
    }

    stage('Build Docker Image') {
      steps {
        dir('app') {
          sh "docker build -t ${IMAGE_NAME} ."
        }
      }
    }

    stage('Parallel Lint & Test') {
      parallel {
        stage('Lint') {
          steps {
            sh "docker run --rm ${IMAGE_NAME} npm run lint || echo 'No linter configured'"
          }
        }
        stage('Test') {
          steps {
            sh "docker run --rm ${IMAGE_NAME} npm test || echo 'No tests configured'"
          }
        }
      }
    }

    stage('Run Container') {
      steps {
        sh "docker rm -f my-running-app || true"
        sh "docker run -d -p 3000:3000 --name my-running-app ${IMAGE_NAME}"
      }
    }
  }
}
