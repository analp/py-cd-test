def linting_pipeline() {
	return [
		stage('linting') {
			echo "Running linting"
		},
		stage('security') {
			echo "Running security"
		}
	]
}

def tests_pipeline(def interpreter, def cov_option, def all) {
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
	checkout scm
  
	def stages = linting_pipeline()

	def tests_branches = [:]
	for (interpreter in interpreters) {
		tests_branches[interpreter] = tests_pipeline(interpreter, "nocov", false)
	}
	stages.push(
		stage('tests') {
			parallel(tests_branches)
		}
	)

	return stages
}

def full_build() {
	checkout scm

  def stages = linting_pipeline()

	def tests_branches = [:]
	for (interpreter in interpreters) {
		tests_branches[interpreter] = tests_pipeline(interpreter, "nocov", false)
	}
	stages.push(
		stage('tests') {
			parallel(tests_branches)
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

	switch(branch) {
		case 'master':
			if (!isTimerTriggered) {
				full_build()

				stage('build package') {
					echo "Building package"
				}

				deleteDir()
			}
			break

		case 'develop':
			if (isTimerTriggered) {
				full_build()
				deleteDir()
			}
			break
		default:
			if (!isTimerTriggered) {
				quick_build("nocov")
				deleteDir()
			}
	}
}
