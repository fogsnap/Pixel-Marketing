pipeline {
	agent {
		node {
			label 'master'
		}
	}
	
	options{
		timestamps()
	}
	
	
	stages{
		stage("Checkout, Test & Publish") {
			steps{
				snDevOpsStep()
				checkout scm
				sh 'mvn clean test'
				
				step([$class : 'Publisher', reportFilenamePattern : '**/testng-results.xml'])
				getCurrentBuildFailedTests()
			}
		}
		
		stage("Email"){
			steps{
				emailext (to: 'mohansairam423@gmail.com', replyTo: 'mohansairam423@gmail.com', subject: "Email Report from - '${env.JOB_NAME}' ", body: readFile("target/surefire-reports/emailable-report.html"), mimeType: 'text/html');
			}
		}
	}
	

}

def getCurrentBuildFailedTests() {
					    def failedTests = []
					    def build = currentBuild.build()

					    def action = build.getActions(hudson.plugins.testng.TestNGTestResultBuildAction.class)
					    if (action) {
						def failures = build.getAction(hudson.plugins.testng.TestNGTestResultBuildAction).getFailedTests()
						println "${failures.size()} Failed Test Results Found"
						def total = build.getAction(hudson.plugins.testng.TestNGTestResultBuildAction).getTotalCount()
						println "${total.size()} Test Results Found"
						echo "executing"
						for (def failure in failures) {
						    failedTests.add(['name': failure.name, 'url': failure.url, 'details': failure.errorDetails])
						}
					    }

					    return failedTests
					}
