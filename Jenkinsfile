

podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: gradle
        image: gradle:6.3-jdk14
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt        
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Build a gradle project') {
      git 'https://github.com/cassandrareed26/week6.git'
      container('gradle') {
        stage('Build a gradle project') {
          sh '''
          chmod +x gradlew
          ./gradlew build
          mv ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
          '''
        }
      }
    }

    stage('Build Java Image') {
      container('kaniko') {
        stage('Build a gradle project') {
	 echo "I am the ${env.BRANCH_NAME} branch"
	  if (env.BRANCH_NAME == master) then {
		  try{
			  sh '''
                          echo 'FROM openjdk:8-jre' > Dockerfile
                          echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
                          echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                          mv /mnt/calculator-0.0.1-SNAPSHOT.jar .
                          /kaniko/executor --context `pwd` --destination creed26/calculator:1.0
                          '''
		  } catch (Exception E) {
                  echo 'Failure detected'
              }
		  if (env.BRANCH_NAME == feature) then {
	            try{
			  sh '''
                          echo 'FROM openjdk:8-jre' > Dockerfile
                          echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
                          echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                          mv /mnt/calculator-0.0.1-SNAPSHOT.jar .
                          /kaniko/executor --context `pwd` --destination creed26/calculator-feature:0.1
                          '''
		    } catch (Exception E) {
                      echo 'Failure detected'
              }
			  fi {
				  echo "Must be the playground branch"
			  }
        
        }
      }
    }
      stage('Unit test') {
          echo "I am the ${env.BRANCH_NAME} branch"
          if (env.BRANCH_NAME != playground)
          {
              try{
                  sh '''
                  pwd
                  chmod +x gradlew
                  ./gradlew test
                  '''
              } catch (Exception E) {
                  echo 'Failure detected in Unit test'
              }
          }
      }
      stage('Code Coverage') {
          echo "I am the ${env.BRANCH_NAME} branch"
          if (env.BRANCH_NAME == 'master'){
              try
              {
                  sh '''
                  ./gradlew jacocoTestCoverageVerification
	               ./gradlew jacocoTestReport
	               '''
              } catch (Exception E) {
                  echo 'Failure detected in code coverage'
              }
              
              // from the HTML publisher plugin
              // https://www.jenkins.io/doc/pipeline/steps/htmlpublisher/
              publishHTML (target: [
                  reportDir: 'build/reports/tests/test',
                  reportFiles: 'index.html',
                  reportName: "JaCoCo Report"
                  ])
          }
      }
  }
}
