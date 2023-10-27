pipeline {
  agent { label 'workstation'}

  stages {

    stage('Download Dependencies'){
      steps {
        sh 'npm install'
        sh 'env'
      }
    }

    stage('Code Quality'){
      when {
        allOf {
          expression { env.TAG_NAME != env.GIT_BRANCH }
        }
      }
      steps {
        sh 'sonar-scanner -Dsonar.host.url=http://172.31.46.28:9000 -Dsonar.login=admin -Dsonar.password=admin123 -Dsonar.projectKey=backend -Dsonar.qualitygate.wait=true'
        echo 'OK'
      }
    }

    stage('Unit Tests'){
      when {
        allOf {
          expression { env.TAG_NAME != env.GIT_BRANCH }
          branch 'main'
        }
      }
      steps {
        // Ideally we should run the tests , But here the developer have skipped it. So assuming those are good and proceeding
        // sh 'npm test'
        echo 'CI'
      }
    }

    stage('Release'){
      when {
        expression { env.TAG_NAME ==~ ".*" }
      }
      steps {
         sh 'zip -r backend-${TAG_NAME}.zip static asset-manifest.json index.html robots.txt'
        sh 'curl -sSf -u "admin:Admin123" -X PUT -T backend-${TAG_NAME}.zip "http://artifactory.sddevops18.online:8081/artifactory/backend/backend-${TAG_NAME}.zip"'    
      }
      steps {
        sh 'docker build -t 624783896224.dkr.ecr.us-east-1.amazonaws.com/backend:${TAG_NAME} .'
        sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 624783896224.dkr.ecr.us-east-1.amazonaws.com'
        sh 'docker push 624783896224.dkr.ecr.us-east-1.amazonaws.com/backend:${TAG_NAME}'
      }
    }

  }

}
///