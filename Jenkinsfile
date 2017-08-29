properties([
	pipelineTriggers([
		cron('0 */3 * * *'),
		pollSCM('H/5 * * * *')
	])
])

node() {
	stage('linting') {
		echo "Running linting"
	}

	stage('security') {
		echo "Running security"
	}

	stage('unit tests') {
		echo "Running unit tests with coverage"
	}

   	stage('integration tests') {
   		echo "Running integration tests"
   	}

   	stage('e2e tests') {
   		echo "Running e2e integration tests"
   	}

   	stage('coverage report') {
   		echo "Runnning coverage report"
	}
}
