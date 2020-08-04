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
                def changeLogSets = currentBuild.changeSets
                for (int i = 0; i < changeLogSets.size(); i++) {
                    def entries = changeLogSets[i].items
                    for (int j = 0; j < entries.length; j++) {
                        def entry = entries[j]
                        entry.getAffectedPaths().each {
                            echo "Change detected: ${it}"   
                        }
                    }
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh './jenkins/scripts/deliver.sh' 
            }
        }
    }
}