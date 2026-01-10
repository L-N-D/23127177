pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/yourname/devsecops-app.git'
      }
    }

    stage('SAST - Semgrep') {
      steps {
        sh '''
        pip install semgrep
        semgrep scan --config=auto
        '''
      }
    }

    stage('Build & Deploy') {
      steps {
        sh '''
        docker-compose down
        docker-compose up -d --build
        '''
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh '''
        docker run --rm owasp/zap2docker-stable \
        zap-baseline.py -t http://116.118.60.232:3000
        '''
      }
    }
  }
}
