pipeline {
  agent any

  tools {
    maven 'maven'
  }

  environment {

    BUILD_NUMBER = "${BUILD_NUMBER}"

  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/parthasm95/simple-java-maven-app.git'
        sh 'mvn -B -DskipTests clean package'
      }
      post {
        success {
          archiveArtifacts 'target/*.jar'
        }
      }
    }
    
    stage("Run Unit Tests") {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage("Deploy to QA") {
      steps {
        echo("deploy to qa done")
      }
    }

    stage('Run Regression Tests') {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE'){
        	git 'https://github.com/parthasm95/Automation-API-RestAssured.git'
        	sh "mvn clean test -Denv=${envName}"
        }
      }
    }
    
    stage('Publish Allure Reports') {
      steps {
        script {
          allure([
            includeProperties: false,
            jdk: '',
            properties: [],
            reportBuildPolicy: 'ALWAYS',
            results: [[path: '/allure-results']]
           ])
         }
       }
     }

    stage('Publish Regression Extent Report') {
      steps {
        publishHTML([allowMissing: false,
          alwaysLinkToLastBuild: false,
          keepAll: false,
          reportDir: 'target',
          reportFiles: 'APIExecutionReport.html',
          reportName: 'API HTML Regression Extent Report',
          reportTitles: ''
        ])
      }
    }

  }
}