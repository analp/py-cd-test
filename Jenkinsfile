def quick_build(def cov_option) {
	return [
		stage('linting') {
			echo "Running linting"
		},
		stage('security') {
			echo "Running security"
		},
		stage('unit tests') {
			def branches = [:]
			branches["pypy3"] = {
				node() {
					echo "Running unit test in pypy3-${cov_option}"
				}
			}
			branches["py36"] = {
				node() {
					echo "Running unit test in py36-${cov_option}"
				}
			}
			parallel branches
		}
	]
}

def full_build() {
	def stages = quick_build("cov")
	stages.push(
		stage('integration tests') {
			def branches = [:]
			branches["pypy3"] = {
				node() {
					echo "Running integration test in pypy3-cov"
				}
			}
			branches["py36"] = {
				node() {
					echo "Running integration test in py36-cov"
				}
			}
			parallel branches
		}
	)

	stages.push(
		stage('e2e tests') {
			echo "Running e2e tests"
		}
	)

	stages.push(
		stage('coverage report') {
			echo "Running coverage report"
		}
	)

	return stages
}

properties([
	pipelineTriggers([
		cron('H */3 * * *'),
		pollSCM('H/5 * * * *')
	])
])

branch = env.BRANCH_NAME

@NonCPS
def isTimerTriggered() {
	def timerTrigger = currentBuild.rawBuild.getCause(hudson.triggers.TimerTrigger$TimerTriggerCause)
	return !!timerTrigger
}

node() {
	def isTimerTriggered = isTimerTriggered() 

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
}
