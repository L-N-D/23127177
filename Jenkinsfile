pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/L-N-D/23127177.git'
      }
    }

    stage('SAST - Semgrep') {
      steps {
        sh '''
          docker run --rm -v "$PWD:/src" semgrep/semgrep semgrep scan --config=auto --exclude zap-report.html
        '''
      }
    }

    stage('Build & Deploy HTML') {
      steps {
        sh '''
          docker-compose down --remove-orphans || true
          docker rm -f web_html || true
          docker-compose up -d --build
          sleep 10
        '''
      }
    }

    stage('DAST - OWASP ZAP') {
      steps {
        sh '''
          docker run --rm --user root \
            --network host \
            -v "$PWD:/zap/wrk:rw" \
            zaproxy/zap-stable \
            zap-baseline.py -t https://iostream.store/ -r zap-report.html -m 1 -I
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'zap-report.html', allowEmptyArchive: true
    }
  }
}
