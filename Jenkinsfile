pipeline {
  agent {
    kubernetes {
      yaml """
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: semgrep
            image: 'returntocorp/semgrep-agent:v1'
            command:
            - cat
            tty: true
        """
      defaultContainer 'semgrep'
    }
  }

  environment {
    if (env.CHANGE_ID) {
      SEMGREP_PR_ID=pullRequest.id
      SEMGREP_PR_TITLE=pullRequest.title
    }
    // secrets for Semgrep org ID and auth token
    SEMGREP_APP_TOKEN     = credentials('SEMGREP_APP_TOKEN')
    SEMGREP_DEPLOYMENT_ID = credentials('SEMGREP_DEPLOYMENT_ID')
    
    // environment variables for semgrep_agent (for findings / analytics page)
    // remove .git at the end
    SEMGREP_REPO_URL = env.GIT_URL.replaceFirst(/^(.*).git$/,'$1')
    SEMGREP_BRANCH = "${GIT_BRANCH}"
    SEMGREP_JOB_URL = "${BUILD_URL}"
    // remove SCM URL + .git at the end
    SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*).git$/, '$1')
    
    SEMGREP_COMMIT="${GIT_COMMIT}"  # commit SHA being scanned
    
  }

  stages {
    stage('Semgrep_agent') {
      steps{
        sh 'env && python -m semgrep_agent --publish-token $SEMGREP_APP_TOKEN --publish-deployment $SEMGREP_DEPLOYMENT_ID'
      }
   }
  }
}
