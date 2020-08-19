def stepsToRun = [:]

pipeline {
    agent any
    stages {
        stage('Preparation') { steps {
                echo "Starting"
                sleep 5
            }
        }
        stage ("Preparing Parallel Stages"){
            steps {
                script {
                    for (int i = 1; i < 5; i++) {
                        stepsToRun["Step${i}"] = prepareStage("Step${i}")
                    }
                    parallel stepsToRun
                }
            }
        }
        stage('Publishing Logs') { steps {
                echo "Exporting logs"
                sleep 5
            }
        }
        stage('Cleaning Up') { steps {
                echo "final"
                sleep 5
            }
        }
    }
}
def getStatus(name) {
    if (name == 'Step1') {
        println name
        return 'FAILURE'
    } else {
        return 'SUCCESS'
    }
}

def getNumberOfBuilds() {
    return 3
}

def prepareStage(def name) {
    return {
        stage (name) {
            def status = ''
            def full_string = "/var/x /var/y /var/z"
            def arr = full_string.split(" ")
            // Dynamic stage generation
            for (i in arr) {
              stage("Test") {
                  echo "helppppp"
                  println "now got ${i}"
              }
            }
            stage("Deployment") {
                script{
                    status = getStatus(name)
                }

                if (status != 'SUCCESS') {
                    catchError(buildResult: status, stageResult: status) {
                        sh "exit 1"
                    }
                } else {
                    catchError(buildResult: status, stageResult: status) {
                        echo "Deployed"
                    }
                }
            }

            stage("Test running phase") {
                def testsStatus = ''
                if (getStatus(name) != 'SUCCESS') {
                    echo getStatus(name)
                    catchError(buildResult: status, stageResult: status) {
                        sh "exit 1"
                    }
                } else {
                    echo "Running tests"
                    sleep 1
                    // TODO : use testsStatus for the buildResult and stageResult
                    catchError(buildResult: status, stageResult: status) {
                        echo "Finished"
                    }
                }
            }
        }
    }
}