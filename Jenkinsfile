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
					bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=Backend -Dsonar.host.url=http://localhost:9000 -Dsonar.login=4a8a4af591a180ad4029fe68140c7cbc605a39d9  -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/main/test/**,**/model/**,**Application.java,/.mvn/wrapper/MavenWrapperDownloader.java"
					}
				}
		}
		
		stage ('Quality Gate') {
			steps {
				sleep(5)
				timeout(time: 1, unit: 'MINUTES'){
				waitForQualityGate abortPipeline: true
				}
			}
		}
		
		stage ('Deploy Backend') {
			steps {
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
				}
		}
		
		stage ('API Test') {
			steps {
				dir('api-test'){
				git credentialsId: 'GitHubLogin', url: 'https://github.com/jcsaGitHub/tasks-apitest'
				bat 'mvn test'
				}
			}
		}

		stage ('Deploy Frontend') {
			steps {
				dir('frontend'){
				git credentialsId: 'GitHubLogin', url: 'https://github.com/jcsaGitHub/tasks-frontend'
				bat 'mvn clean package'
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
				}
			}
		}
		
		stage ('Functional Test') {
			steps {
				dir('functional-test'){
				git credentialsId: 'GitHubLogin', url: 'https://github.com/jcsaGitHub/tasks-functionaltest'
				bat 'mvn clean test'
				}
			}
		}


	}
}