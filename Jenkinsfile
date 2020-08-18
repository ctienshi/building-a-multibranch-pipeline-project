def stepsToRun = [:]
myVar = 'initial_value'

pipeline {
    agent none
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

def prepareStage(def name) {
    var = 'SUCCESS'
    return {
        stage (name) {
            stage("Deployment") {
                script{

                }
                if (getStatus(name) != 'SUCCESS') {
                    catchError(buildResult: getStatus(name), stageResult: getStatus(name)) {
                        status = "FAILURE"
                        sh "exit 1"
                    }
                } else {
                    catchError(buildResult: getStatus(name), stageResult: getStatus(name)) {
                        echo "Deployed"
                    }
                }
                echo "Deploying the product"
            }

            stage("Test running phase") {
                if (getStatus(name) != 'SUCCESS') {
                    echo getStatus(name)
                    catchError(buildResult: getStatus(name), stageResult: getStatus(name)) {
                        status = "FAILURE"
                        sh "exit 1"
                    }
                } else {
                    echo "Running tests"
                    sleep 1
                    catchError(buildResult: getStatus(name), stageResult: getStatus(name)) {
                        echo "Finished"
                    }
                }
            }
        }
    }
}