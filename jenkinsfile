import groovy.transform.Field
@Field
def nodeDetails = []; //add nodedetail from csv
@Field
def nodeBuildMap = [:]
def headers = []
def deleteDirExecuted = []

new File("/tmp/test.csv").readLines().eachWithIndex { row, rowIndex ->
    if (rowIndex == 0) { headers = row.split(',') }
    else {
        def tmpMap = [:]
        def cells = row.split(',').eachWithIndex { cell, cellIndex ->
            tmpMap[headers[cellIndex]] = cell
        }
        nodeDetails.add(tmpMap)
    }
}
println(nodeDetails)
for (nodeList in nodeDetails) {
    def windowLabel = nodeList["windows_vm"]
    def linuxLabel = nodeList["linux_vm"]
    def appName = nodeList["app_name"]
    // Create a map to pass in to the 'parallel' step so we can fire all the builds at once
    nodeBuildMap[appName] = {
        stage("window") {
            //running on windows node
            node(windowLabel) {
                bat 'dir' //sample command execute on window node
            }
        }
        stage("linux") {
            //running on linux node
            node(linuxLabel) {
                sh 'ls' //sample command execute on linux node
            }
        }
    }
}
pipeline {
    agent none
    stages {
        stage("Build") {
            steps {
                script {
                    parallel nodeBuildMap
                }
            }
        }
        stage("result") {
            steps {
                println "Combining the result"
            }
        }
    }
}
