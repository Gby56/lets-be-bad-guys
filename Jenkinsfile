


pipeline {
  agent {
    kubernetes {
      yaml """
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: semgrep
            image 'returntocorp/semgrep-agent:v1'
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
    SEMGREP_BRANCH = "${GIT_BRANCH}"
    SEMGREP_JOB_URL = "${BUILD_URL}"
    // remove SCM URL + .git at the end
    SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*).git$/, '$1')
  }

  stages {
    stage('Semgrep_agent') {
      steps{
        sh 'python -m semgrep_agent --publish-token $SEMGREP_APP_TOKEN --publish-deployment $SEMGREP_DEPLOYMENT_ID'
      }
   }
  }
}
