openshift.withCluster() {
  openshift.withCredentials('eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJqZW5raW5zLWNpIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImplbmtpbnMtdG9rZW4tNjI4a3IiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiamVua2lucyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImU4OTk2OWNkLTk0Y2EtMTFlOC1hNjNmLTAwNTA1NmEyMGYyOCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpqZW5raW5zLWNpOmplbmtpbnMifQ.EOcO29nDecXpXU6f1nHoYf3Ocs3T0stShglO6Oj86pacYHlUW4pnXkHIWgMgSSugFnQDxa-TSrAdW7ELd5p1oGUIzYM4na9FUDQtISKbD_RpbWPYcdZ7P0JoVw1cWf6lxooVEreyw8-cBsOdM4Smk8mMVqoEHEwNR7Vhl3kw1nG8hfmNm7_cyw--HWc_4_3_ZS8UY8jE9i8xJmh7I71V6oQw0YiiGkMZVx2sTAhHx30jjymlWqdjzZ4TZmqIeMD-WZjI-8WD1aLQkAi3Ck9QSfZi4R_Y_-tcqlHlD_aIvV2Z6pEOLxQG98IMPsEB2pmGf70QLcsRpYGwgly1DU8kwQ') {
  def PROJECT_NAME = "elm-sample-service"
  def cm


  openshift.withProject(PROJECT_NAME) {
   def APP_1_GIT_URL = "https://github.com/rahmed-rh/elm-service"
   echo "Hello from project ${openshift.project()} in cluster ${openshift.cluster()}"

   // Mark the code checkout 'stage'....
   stage('Read CM') {

    def cmSelector = openshift.selector("configmap", "pipeline-app-config")
    def cmExists = cmSelector.exists()

    if (cmExists) {
     cm = cmSelector.delete()
    }
    def cmappconfig = [
     [
      "kind": "ConfigMap",
      "apiVersion": "v1",
      "metadata": [
       "name": "pipeline-app-config"
      ],
      "data": [
       "sample-app-git-url": "${APP_1_GIT_URL}",
      ]
     ]
    ]
    cm = openshift.create(cmappconfig).object()


    echo "The CM is ${cm}"
    echo "The fis-1-app-git-url is ${cm.data['sample-app-git-url']}"
   }

   node('maven') {
    // Mark the code checkout 'stage'....
    stage('Checkout') {

      // Get some code from a GitHub repository
      git branch: "master", url: cm.data['sample-app-git-url']
     }
     	// Mark the code build 'stage'....
    stage('Maven Build') {
	     // Run the maven build
	     sh "mvn clean install -Popenshift"
    }
    stage('Deploy to DEV') {
     	// Run the fabric8
     	sh "mvn fabric8:deploy -Popenshift"
    }

   }

  }
}
}
