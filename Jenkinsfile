node {
  checkout scm 
  stage('Package') {
    dir('webapp') {
      bat 'mvn clean package -DskipTests'
    }
  }

  stage('Create Docker Image') {
    dir('webapp') {
      docker.build("localhost:5000/docker-jenkins-pipeline:${env.BUILD_NUMBER}")
    }
  }

  stage ('Run Application') {
    try {
      // Start database container here
      // sh 'docker run -d --name db -p 8091-8093:8091-8093 -p 11210:11210 arungupta/oreilly-couchbase:latest'

      // Run application using Docker image
      bat "DB=`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db`"
      bat "docker run -e DB_URI=$DB localhost:5000/docker-jenkins-pipeline:${env.BUILD_NUMBER}"

      // Run tests using Maven
      //dir ('webapp') {
      //  sh 'mvn exec:java -DskipTests'
      //}
    } catch (error) {
    } finally {
      // Stop and remove database container here
      //sh 'docker-compose stop db'
      //sh 'docker-compose rm db'
    }
  }

  stage('Run Tests') {
    try {
      dir('webapp') {
        bat "mvn test"
        docker.build("localhost:5000/docker-jenkins-pipeline:${env.BUILD_NUMBER}").push()
      }
    } catch (error) {

    } finally {
      junit '**/target/surefire-reports/*.xml'
    }
  }
  
  stage('Run Kubernetes') {
    try { 
        bat "kubectl apply -f y4.yaml" 
      
    } catch (error) {

    } finally {
     
    }
  }
  
}
