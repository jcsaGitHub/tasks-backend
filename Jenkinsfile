pipeline {
	agent any
	stages {
		stage ('Build Backend') {
			steps {
				bat 'mvn clean package -DskipTests=true'
				}
		}

		stage ('Unit Tests') {
			steps {
				bat 'mvn test'
				}
		}

		stage ('Sonar Analysis') {
			environment {
				scannerHome = tool 'SONAR_SCANNER'
			}
			steps {
				withSonarQubeEnv('SONAR_LOCAL'){
						bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=Backend -Dsonar.host.url=http://localhost:9000 -Dsonar.login=4a8a4af591a180ad4029fe68140c7cbc605a39d9 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**, **/src/main/test/**, **/model/**,**Application.java"
					}
				}
		}
		
		stage ('Quality Gate') {
			steps {
				timeout(time: 1, unit: 'MINUTES'){
				waitForQualityGate abortPipeline: true
				}
			}
		}

	}
}