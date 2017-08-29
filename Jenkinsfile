def quick_build(def coverage) {
	return [
		stage('linting') {
			echo "Running linting"
		},
		stage('security') {
			echo "Running security"
		},
		stage('unit tests') {
			echo "Running unit tests ${coverage}"
		}
	]
}

def full_build() {
	def stages = quick_build("with coverage")
	stages.push(
		stage('integration tests') {
			echo "Running integration tests with coverage"
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
def timerTrigger = currentBuild.rawBuild.getCause(hudson.triggers.TimerTrigger$TimerTriggerCause)

node() {
	switch(branch) {
		case 'master':
			if (!timerTrigger) {
				full_build()

				stage('build package') {
					echo "Building package"
				}
			}
			break

		case 'develop':
			if (timerTrigger) {
				full_build()
			}
			break
		default:
			if (!timerTrigger) {
				quick_build("without coverage")
			}
	}
}
