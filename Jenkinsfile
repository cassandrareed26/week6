podTemplate(containers: [
  containerTemplate(
    name: 'gradle',
    image: 'gradle:6.3-jdk14',
    command: 'sleep',
    args: '30d'
    ),
  ]) {
    node(POD_LABEL) {
      stage('Run pipeline against a gradle project - test MAIN') {
	container('gradle') {
          stage('Build a gradle project') {
	    git 'https://github.com/cassandrareed26/week6.git'
            sh '''
            cd sample1
            chmod +x gradlew
            ./gradlew test
            '''
            echo "I am the ${env.BRANCH_NAME} branch"
          }
          stage('Code coverage') {
	    sh 'printenv'
            echo "My CC branch is: ${env.CHANGE_BRANCH}"
            if (env.BRANCH_NAME == "main") {
              echo "I am the ${env.BRANCH_NAME} branch"
            }
          }
        }
      }   
    }
}
