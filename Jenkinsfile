def run_tests(def interpreter, def cov_option, def all) {
	return {	
		node() {
			stage('unit tests') {
				echo "Running unit tests in ${interpreter}-${cov_option}"
			}
			
			if(all) {
				stage('integration tests') {
					echo "Running integration tests in ${interpreter}-${cov_option}"
				}
				stage('e2e tests') {
					echo "Running e2e tests in ${interpreter}-${cov_option}"
				}
			}
		}
	}
}

interpreters = ["pypy3", "py36"]

def quick_build(def cov_option) {
	def tests = [:]
	for (interpreter in interpreters) {
		tests[interpreter] = run_tests(interpreter, "nocov", false)
	}

	return [
		stage('linting') {
			echo "Running linting"
		},
		stage('security') {
			echo "Running security"
		},
		stage('tests') {
			parallel(tests)
		}
	]
}

def full_build() {
	def tests = [:]
	for (interpreter in interpreters) {
		tests[interpreter] = run_tests(interpreter, "nocov", false)
	}

	return [
		stage('linting') {
			echo "Running linting"
		},
		stage('security') {
			echo "Running security"
		},
		stage('tests') {
			parallel(tests)
		},
		stage('coverage report') {
			echo "Running coverage report"
		}
	]
}

properties([
	pipelineTriggers([
		cron('H */3 * * *'),
		pollSCM('H/5 * * * *')
	]),
	buildDiscarder(logRotator(daysToKeepStr: '30'))
])

branch = env.BRANCH_NAME

@NonCPS
def isTimerTriggered() {
	def timerTrigger = currentBuild.rawBuild.getCause(hudson.triggers.TimerTrigger$TimerTriggerCause)
	return !!timerTrigger
}

node() {
	def isTimerTriggered = isTimerTriggered()

	checkout scm

	switch(branch) {
		case 'master':
			if (!isTimerTriggered) {
				full_build()

				stage('build package') {
					echo "Building package"
				}
			}
			break

		case 'develop':
			if (isTimerTriggered) {
				full_build()
			}
			break
		default:
			if (!isTimerTriggered) {
				quick_build("nocov")
			}
	}

	stage('Cleanup') {
		deleteDir()
	}
}
