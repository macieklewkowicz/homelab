pipeline {
  agent none

  parameters {
    string(name: "GERRIT_CREDENTIALS_ID", defaultValue: "gerrit")
  }

  stages {
    stage('lint') {
      agent {
        kubernetes {
          yaml '''
            spec:
              containers:
              - name: jnlp
                image: 'harbor.k8s.lan/smol/jenkins-inbound-agent'
                args: ['\$(JENKINS_SECRET)', '\$(JENKINS_NAME)']
              - name: kube-score
                image: zegl/kube-score:v1.18.0
                command:
                - sleep
                args:
                - 99d
              - name: xml
                image: leplusorg/xml:main
                command:
                - sleep
                args:
                - 99d
          '''
        } // kubernetes
      } // agent

      steps {
        container('xml') {
          sh """
            xmllint default.xml
          """
        } // container 'xml'
      } // steps
    } // stage 'lint'
  } // stages

  post {
    success {
      gerritReview \
        labels: [
          'Code-Review': 0,
          'Verified': 1
        ],
        message: "${env.BUILD_URL}"
    } // success

    unstable {
      gerritReview \
        labels: [
          'Code-Review': -1,
          'Verified': 0
        ],
        message: "${env.BUILD_URL}"
    } // unstable

    failure {
      gerritReview \
        labels: [
          'Code-Review': -1,
          'Verified': -1
        ],
        message: "${env.BUILD_URL}"
    } // failure
  } // post
} // pipeline
