pipeline {
  agent none
  stages {
    stage('build-worker') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        echo 'Build worker app.'
        dir(path: 'worker') {
          sh 'mvn compile'
        }

      }
    }

    stage('test-worker') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        echo 'Running unit test on worker app.'
        dir(path: 'worker') {
          sh 'mvn clean test'
        }

      }
    }

    stage('package-worker') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        branch 'master'
        changeset '**/worker/**'
      }
      steps {
        echo 'Package worker app.'
        dir(path: 'worker') {
          sh 'mvn package -DskipTests'
          archiveArtifacts(artifacts: '**/target/*.jar', fingerprint: true)
        }

      }
    }

    stage('docker-package-worker') {
      agent any
      when {
        branch 'master'
        changeset '**/worker/**'
      }
      steps {
        echo 'Package worker app with docker.'
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-baugereau') {
            def workerImage = docker.build("baugereau/worker:v${env.BUILD_ID}", "./worker")
            workerImage.push()
            workerImage.push("worker")
          }
        }

      }
    }

    stage('build-result') {
      agent {
        docker {
          image 'node:8.9.0-alpine'
        }

      }
      when {
        changeset '**/result/**'
      }
      steps {
        echo 'Build result app.'
        dir(path: 'result') {
          sh 'npm install'
          sh 'npm ls'
        }

      }
    }

    stage('test-result') {
      agent {
        docker {
          image 'node:8.9.0-alpine'
        }

      }
      when {
        changeset '**/result/**'
      }
      steps {
        echo 'Running unit test on result app.'
        dir(path: 'result') {
          sh 'npm install'
          sh 'npm test'
        }

      }
    }

    stage('docker-package-result') {
      agent any
      when {
        branch 'master'
        changeset '**/result/**'
      }
      steps {
        echo 'Package result app with docker.'
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-baugereau') {
            def workerImage = docker.build("baugereau/result:v${env.BUILD_ID}", "./result")
            workerImage.push()
            workerImage.push("result")
          }
        }

      }
    }

    stage('build-vote') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        echo 'Build vote app.'
        dir(path: 'vote') {
          sh 'pip install -r requirements.txt'
        }

      }
    }

    stage('test-vote') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        echo 'Running unit test on vote app.'
        dir(path: 'vote') {
          sh 'pip install -r requirements.txt'
          sh 'nosetests -v'
        }

      }
      } 
 
    stage('integration-test-vote') {
      agent any 
      when {
        changeset '**/vote/**'
	branch 'master'
      }
      steps {
        echo 'Running integration test on vote app.'
        dir(path: 'vote') {
          sh './integration_test.sh'
        }

      }
    }  

    stage('docker-package-vote') {
      agent any
      when {
        branch 'master'
        changeset '**/vote/**'
      }
      steps {
        echo 'Package vote app with docker.'
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-baugereau') {
            def workerImage = docker.build("baugereau/vote:v${env.BUILD_ID}", "./vote")
            workerImage.push()
            workerImage.push("vote")
          }
        }

      }
    }

/*
    stage('Sonarqube') {
      agent any
      when{
        branch 'master'
      }
      tools {
        jdk "JDK11" // the name you have given the JDK installation in Global Tool Configuration
      }

      environment{
        sonarpath = tool 'SonarScanner'
      }

      steps {
            echo 'Running Sonarqube Analysis..'
            withSonarQubeEnv('sonar-example-voting-app') {
              sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties -Dorg.jenkinsci.plugins.durabletask.BourneShellScript.HEARTBEAT_CHECK_INTERVAL=86400"
            }
      }
    }

    stage("Quality Gate") {
        steps {
            timeout(time: 1, unit: 'HOURS') {
                // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                // true = set pipeline to UNSTABLE, false = don't
                waitForQualityGate abortPipeline: true
            }
        }
    }
*/

    stage('Deploy to Dev') {
      agent any
      when {
        branch 'master'
      }
      steps {
        sh 'docker-compose up -d'
      }
    }

  }
  post {
    always {
      echo 'Pipeline is completed.'
    }

  }
}
