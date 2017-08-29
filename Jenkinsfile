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
	full_build()

	switch(branch) {
		case 'master':
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

		case 'develop':
			if (timerTrigger) {
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
