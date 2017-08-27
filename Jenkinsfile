branch = env.BRANCH_NAME

node() {
	stage('linting') {
		echo "Running linting"
	}

	stage('security') {
		echo "Running security"
	}

	switch (branch) {
		case 'develop':
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

		case 'master':
			stage('package') {
				echo "Building package"
			}
			break

		default:
			stage('unit tests') {
				echo "Running unit tests without coverage"
			}
	}
}
