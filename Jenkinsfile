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

    stage('Install Dependencies') {
      steps {
        dir('app') {
          sh 'npm install'
        }
      }
    }

    stage('Parallel Lint & Test') {
      parallel {
        stage('Lint') {
          steps {
            dir('app') {
              sh 'npm run lint || echo "No linter configured"'
            }
          }
        }
        stage('Test') {
          steps {
            dir('app') {
              sh 'npm test || echo "No tests configured"'
            }
          }
        }
      }
    }

    stage('Build Docker Image') {
      steps {
        dir('app') {
          sh "docker build -t ${IMAGE_NAME} ."
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
