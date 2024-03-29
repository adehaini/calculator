pipeline {
     agent any
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               steps {
                    sh "./gradlew test"
               }
          }

	  stage("Code coverage") {
     		steps {
          	     sh "./gradlew jacocoTestReport"
		     publishHTML (target: [
			reportDir: 'build/reports/jacoco/test/html',
               		reportFiles: 'index.html',
               		reportName: "JaCoCo Report"
          	     ])
          	     sh "./gradlew jacocoTestCoverageVerification"
     		}
	  }
	  stage("Static code analysis") {
     		steps {
          	    sh "./gradlew checkstyleMain"
		    publishHTML (target: [
  		    	reportDir: 'build/reports/checkstyle/',
     		   	reportFiles: 'main.html',
     			reportName: "Checkstyle Report"
		    ])
     		}
	  }

	  stage("Package") {
     		steps {
          	    sh "./gradlew build"
     		}
	  }

	   stage("Docker build") {
     		steps {
          	    sh "docker build -t fluidout/calculator::${BUILD_TIMESTAMP} ."
     		}
	   }	

	   stage("Docker login") {
                steps {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub-credentials',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                    sh "docker login --username $USERNAME --password $PASSWORD"
                    }
               }
          }


	   stage("Docker push") {
     		steps {
          	    sh "docker push fluidout/calculator:${BUILD_TIMESTAMP}"
     		}
	   }		


     }
}
