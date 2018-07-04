openshift.withCluster() {
  def PROJECT_NAME = "test-app"
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
	     sh "mvn clean compile"
    }
    stage('Deploy to DEV') {
	    //echo "Deleting OLD JDG Env if exist"
	    //openshift.selector( 'all', [ application: cm.data['fis-1-app-name'] ] ).delete()
	    //openshift.selector( 'pvc', [ application: cm.data['fis-1-app-name'] ] ).delete()
     	// Run the fabric8
     	sh "mvn fabric8:deploy"
    }

   }

  }
}
