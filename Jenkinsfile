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

    // secrets for Semgrep org ID and auth token
    SEMGREP_APP_TOKEN     = credentials('SEMGREP_APP_TOKEN')
    SEMGREP_DEPLOYMENT_ID = credentials('SEMGREP_DEPLOYMENT_ID')
    
    // environment variables for semgrep_agent (for findings / analytics page)
    // remove .git at the end
    SEMGREP_REPO_URL = env.GIT_URL.replaceFirst(/^(.*).git$/,'$1')
    SEMGREP_BRANCH = "${CHANGE_BRANCH}"
    SEMGREP_JOB_URL = "${BUILD_URL}"
    // remove SCM URL + .git at the end
    SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*).git$/, '$1')
    
    SEMGREP_COMMIT = "${GIT_COMMIT}"
    SEMGREP_PR_ID = "${env.CHANGE_ID}"
    BASELINE_BRANCH = "${env.CHANGE_TARGET}"

  }

  stages {
    stage('Semgrep_agent') {
      when {
        expression { env.CHANGE_ID && env.BRANCH_NAME.startsWith("PR-") }
      }
      steps{
        sh 'env && git fetch origin master && python -m semgrep_agent --baseline-ref origin/$BASELINE_BRANCH --publish-token $SEMGREP_APP_TOKEN --publish-deployment $SEMGREP_DEPLOYMENT_ID'
      }
   }
  }
}
