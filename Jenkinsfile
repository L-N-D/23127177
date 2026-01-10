pipeline {
  agent any

  environment {
    APP_URL = "http://116.118.60.232:3000"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('SAST - Semgrep') {
      steps {
        sh '''
          pip3 install --user semgrep || true
          ~/.local/bin/semgrep scan --config=auto || true
        '''
      }
    }

    stage('Build & Deploy') {
      steps {
        sh '''
          docker-compose down || true
          docker-compose up -d --build
        '''
      }
    }

    stage('Wait for App') {
      steps {
        sh '''
          echo "Waiting for app to be ready..."
          sleep 15
        '''
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh '''
          docker run --rm owasp/zap2docker-stable \
          zap-baseline.py -t $APP_URL || true
        '''
      }
    }
  }
}
