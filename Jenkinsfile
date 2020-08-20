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
    return "ActiveMQ RabbitMQ VFS"
}

def prepareStage(def name) {
    return {
        stage (name) {
            def status = ''
            def full_string = getNumberOfBuilds()
            def arr = full_string.split(" ")
            // Dynamic stage generation
            def dependencyNo = 0
            def previousDependencyStatus = 'SUCCESS'
            for (i in arr) {
                stage("${i} Deployment" ) {
                    if (previousDependencyStatus == 'SUCCESS') {
                        echo "Deploying dependencies"
                        println "now got ${i}"
                        dependencyNo++
                        echo "${dependencyNo}"
                        echo "${arr.length}"
//                         previousDependencyStatus = 'FAILURE'
                    } else {
                        catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                            sh "exit 1"
                        }
                    }
              }
              // if success increase dependencyNo
            }
            stage("Product Deployment") {
                script{
                    status = getStatus(name)
                }
                echo "${dependencyNo}"
                if (arr.length == dependencyNo) {
                // if dependencyNo = number of dependencies only run. Else fail stage
                    if (status != 'SUCCESS') {
                        catchError(buildResult: status, stageResult: status) {
                            sh "exit 1"
                        }
                    } else {
                        catchError(buildResult: status, stageResult: status) {
                            echo "Deployed"
                        }
                    }
                } else {
                    catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                        sh "exit 1"
                    }
                }
            }

            stage("Tests Running Phase") {
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