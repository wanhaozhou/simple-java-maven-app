pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        stage('Optional') {
            when {
                changeset pattern: ".*src/main/java/com/mycompany/app/.*", comparator: "REGEXP"
            }
            steps {
                paths()
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}


static def paths()  {
    def res = currentBuild.changeSets.collectMany {
        it.items.collectMany {
            it.affectedPaths
        }
    }.unique()
    println(res.join('\n'))
}