pipeline {
    
    agent any
    
    tools {
    	maven 'Maven 3.6.1'
    }

    stages{
        stage('build'){
            steps{
                echo 'Build worker app.'
		dir('worker'){
			sh 'mvn compile'
		}
            }
        }
        stage('test'){
            steps{
                echo 'Running unit test on worker app.'
	    	sh 'mvn clean test'
            }
        }
        stage('package'){
            steps{
                echo 'Package worker app.'
	    	sh 'mvn package -DskipTests'
            }
        }
    }
    
    post {
        always {
            echo 'Build pipeline for worker is completed.'
        }
    }
}
