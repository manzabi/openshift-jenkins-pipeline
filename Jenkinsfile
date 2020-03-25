application = "hello-nodejs"
project = "allianz"

minishiftApiURL = "https://192.168.64.6:8443"
minishiftAuthToken = "eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJhbGxpYW56Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImplbmtpbnMtdG9rZW4tZDlkdDkiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiamVua2lucyIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU2OTQ3NTQyLTZlYzAtMTFlYS1hOTM1LTcyMzM3NTMxZDc4NyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDphbGxpYW56OmplbmtpbnMifQ.aj4tAbj0Ds2jzTH6xVN5kQ3XmTUiTsOkZ4NHzXzxqMj2KewdWfXmGvXFN3oKuzNoMtd_dZnnvpPzujpv-eKfQK-0a4UJ26s2ZHhDkLR89CTPmtmLPPVw3UuoWSq2TwB2F27E8lA16LlTcm29v-x2HkzrstP_Rln9iMpI4g7EeXNL81z7nykOSc4HUorWplFzsidVlC3j8agiCdbo3_dTv-lQJbCTopwIPz5z4-xhiE1vPky1bKxyqCBTDHkQsuKKsL1-99Jmt4VSG3QIedj1s5u6K7HvVUaOk0I9UHcXbfv0zaijpDo0mxGQAK_NvnkqD-WAQcV1U8tJ_8e0LTDWgg"

contextDir = "/"
gitBranch = "master"
gitURL = "https://github.com/manzabi/openshift-jenkins-pipeline"
minishiftTemplatePath = "openshift-templates/nodejs.json"


node('nodejs'){

   getJenkinsSACredentials()
    
    sh """
        set +x
        oc login --token=${minishiftAuthToken} ${minishiftApiURL} >/dev/null 2>&1 || echo 'OpenShift login failed'
    """
    
    //git changelog: false, poll: false, url: 'https://github.com/vidhyachari/openshift-hello-nodejs'
    // Checkout the source code
    checkout scm
    
    sh """
      oc process -f ${minishiftTemplatePath} \
        NAME=${application} \
        SOURCE_REPOSITORY_REF=${gitBranch} \
        SOURCE_REPOSITORY_URL=${gitURL} \
        CONTEXT_DIR="/" \
        -n ${project} | oc apply -f - -n ${project}
      """

    //Start openshift build
    sh """
      oc get bc ${application} -n ${project}

      # Cancel any currently running builds
      oc cancel-build bc/${application} -n ${project}

      # Start build and follow logs
      oc start-build ${application} --follow -n ${project}
    """
   
   //Start Deployment
   sh """
      oc rollout cancel dc/${application} -n ${project}
      oc rollout latest dc/${application} -n ${project}
   """
   
}//node

def getJenkinsSACredentials(){

  withCredentials([[$class: 'StringBinding',
    credentialsId: 'jenkins-service-account',
    variable: 'authToken']]) {
    openshiftAuthToken = authToken
  }

}
