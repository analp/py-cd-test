branch = env.BRANCH_NAME

if (env.BRANCH_NAME.equals('master')) {
	coverage = 'cov'
} else {
	coverage = 'no-cov'
}

node() {
	stage('linting') {
		echo "Running linting"
	}

	stage('security') {
		echo "Running security"
	}

	stage('unit tests') {
		echo "Running unit tests"
	}

    if (branch.equals('master)) {
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
}
