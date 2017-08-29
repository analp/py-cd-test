properties([
	pipelineTriggers([
		cron('H */3 * * *'),
		pollSCM('H/5 * * * *')
	])
])

branch = env.BRANCH_NAME

node() {
	stage('linting') {
		echo "Running linting"
	}

	stage('security') {
		echo "Running security"
	}

	switch(branch) {
		case 'master':
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
			break
		default:
			stage('unit tests') {
				echo "Running unit tests without coverage"
			}
	}

	if (branch == 'master') {
		stage('build package') {
			echo "Building package"
		}
	}
}
