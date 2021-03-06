pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
				sh("chmod +x gradlew")
				sh("./gradlew clean build")
            }
        }
		stage('Package') {
            steps {
                echo 'Building..'
				sh("./gradlew bootRepackage")
            }
        }
        stage('Test') {
            steps {
               sh("./gradlew test")
            }
        }
		stage('Build Docker') {
            steps {
                echo 'Buildin docker.'
				withCredentials([usernamePassword(credentialsId: 'docker-repo', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
				sh("sudo docker login -u=$USERNAME -p=$PASSWORD")
				sh("sudo docker build . --tag husamay/rps-backend:0.1.${BUILD_NUMBER}")
		}
            }
        }
	    
	        stage('Test image') {
      steps {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        sh 'sudo docker rm backend-container || true'
        sh 'sudo docker run -t --rm --name backend-container husamay/rps-backend:0.1.${BUILD_NUMBER} &'
        sh 'sleep 5s'
        sh 'sudo docker exec -t backend-container bash -c \'ls -l\''
        sh 'sudo docker stop backend-container'
      }
    }

        stage('Deploy Docker') {
            steps {
                echo 'Deploying 0.1.${BUILD_NUMBER} to repo....'
				sh("sudo docker push husamay/rps-backend:0.1.${BUILD_NUMBER}")
                echo 'Deploying latest tag to repo....'		    
		sh("sudo docker tag husamay/rps-backend:0.1.${BUILD_NUMBER} husamay/rps-backend:latest")
		sh("sudo docker push husamay/rps-backend:latest")
            }
        }
    }
}
