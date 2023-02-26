podTemplate(podRetention: onFailure(), containers: [
    containerTemplate(
        name: 'gradle', image: 'gradle:6.3-jdk14', command: 'sleep', args: '30d'
        ),
    ]) {

    node(POD_LABEL) {
        stage('Run pipeline against a gradle project') {
            // "container" Selects a container of the agent pod so that all shell steps are executed in that container.
            container('gradle') {
                stage('Build a gradle project') {
                    // from the git plugin
                    // https://www.jenkins.io/doc/pipeline/steps/git/
                    git 'https://github.com/cassandrareed26/week6.git'
                    sh '''
                    cd sample1
                    chmod +x gradlew
                    ./gradlew test
                    '''
                }
            
                stage("Code coverage") {
		     when {                    
			branch 'main'
		     }
		     try {
                        sh '''
        	            pwd
               		    cd sample1
                	    ./gradlew jacocoTestCoverageVerification
                        ./gradlew jacocoTestReport
                        '''
                    } catch (Exception E) {
                        echo 'Failure detected'
                    }
               }
                    
                 stage("Checkstyle Test") {
                    when {                    
			branch 'feature'
		     }
		     try {
                        sh '''
        	            pwd
               		    cd sample1
                	    ./gradlew checkstyleMain
                        ./gradlew jacocoTestReport
                        '''
                    } catch (Exception E) {
                        echo 'Failure detected'
                    }

                    // from the HTML publisher plugin
                    // https://www.jenkins.io/doc/pipeline/steps/htmlpublisher/
                    publishHTML (target: [
                        reportDir: 'sample1/build/reports/checkstyle',
                        reportFiles: 'main.html',
                        reportName: "JaCoCo Checkstyle"
                    ])                       
                }
           }
        }
    }
}
