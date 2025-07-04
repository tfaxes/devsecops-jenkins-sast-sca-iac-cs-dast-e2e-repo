pipeline {
  agent any
  tools {
    maven 'Maven_3_8_7'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
    export SONAR_TOKEN="${SONAR_TOKEN}"
    '/Users/craigjohnson/Downloads/Udemy Stuff/devsecops/apache-maven-3.8.7/bin/'mvn \
    -Dmaven.test.failure.ignore verify sonar:sonar \
    -Dsonar.login="${SONAR_TOKEN}" -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://localhost:9000/"
'''
        }
      }
    }
    stage('Build') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("asecurityguru/testeb")
          }
        }
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              sh "/opt/homebrew/bin/snyk container test asecurityguru/testeb"
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh "/Users/craigjohnson/Downloads/Udemy Stuff/devsecops/apache-maven-3.8.7/bin/mvn snyk:test -fn"
        }
      }
    }
    stage('RunDASTUsingZAP') {
      steps {
        sh "/Applications/ZAP.app/Contents/Java/zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout /Users/craigjohnson/Documents/Output.html"
      }
    }

    stage('checkov') {
      steps {
        sh "/Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages/checkov -s -f main.tf"
      }
    }

  }
}
