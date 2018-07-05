node('master') {
  stage('Poll') {
    checkout scm
  }
  stage('Build a Unit test') {
    mvn clean verify -DskipITs;
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar'
  }
  stage('Static Code Analysis') {
    'mvn clean verify sonar:sonar -Dsonar.projectName=hello-world-greeting -Dsonar.projectKey=hello-world-greeting -DprojectVersion=BUILD_NUMBER';
  }
  stage('Integration Test') {
      mvn clean verify -Dsurefire.skip;
    junit '**/target/failsafe-reports/TEST-*.xml'
    archive 'target/*.jar'
  }
  stage('Publish') {
    def server = Artifactory.server 'Default Artifactory Server'
    def uploadSpec = """ {
      "files": {
        {
          "pattern": "target/hello-0.0.1.war",
          "target": "example-project/${BUILD_NUMBER}/",
          "props": "Integration-Tested=Yes;Performance-Tested=No"
        }
      }
    }"""
    server.upload(uploadSpec)
  }
}
