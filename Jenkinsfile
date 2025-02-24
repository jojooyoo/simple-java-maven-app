pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:           
    - name: maven386jdk11
      image: maven:3.8.6-jdk-11
      command: [ 'cat' ]
      tty: true
      resources:
        requests:
          memory: "1024Mi"
          cpu: "500m"
        limits:
          memory: "1024Mi"
          cpu: "500m"
"""
        }
    }

parameters{
    booleanParam(defaultValue: false, description: "run test?", name: "runtest")
    booleanParam(defaultValue: false, description: "run deliver?", name: "rundeliver")
}


    stages {
        stage('Build') { 
            steps {
                container('maven386jdk11') {
                    sh 'mvn -B -DskipTests clean package' 
                }
            }
        }

		stage('Test') {
            when {
                expression { params.runtest == true }
            }
            steps {
                container('maven386jdk11') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Deliver') {
            when {
                expression { params.rundeliver == true }
            }
            steps {
                container('maven386jdk11') {
                    sh './jenkins/scripts/deliver.sh'
                }
            }
        }
    }
}
