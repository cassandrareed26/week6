pipeline {
     agent any
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               when {
                    not { branch 'main' }
          }       
	       steps {
		    echo 'Unit test on not main branch'
                    sh "./gradlew test"
               }
          }
          stage("Code coverage") {
	       when { branch 'main' }
               steps {
		    echo 'Code coverage for only main branch'
                    sh "./gradlew jacocoTestReport"
                    sh "./gradlew jacocoTestCoverageVerification"
               }
          }
     }
}