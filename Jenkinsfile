pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'httpCallumj', passwordVariable: 'password', usernameVariable: 'username')]) {
		    sh 'ls -lR'
		    sh 'echo $PWD'
		    sh 'chmod a+xwr deploy/buildIIB.sh'
		    sh 'deploy/buildIIB.sh $password'
		}
        
      }
    }
  }
}