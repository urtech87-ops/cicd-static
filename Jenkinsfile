// ============================================================
//  Jenkinsfile — the pipeline definition for cicd-static
//  Lives in the repo root, exactly like a GitHub Actions .yml
//  Jenkins reads this to know what stages to run.
// ============================================================
pipeline {

    // WHERE it runs. 'any' = run on the Jenkins machine itself
    // (your laptop). No separate build agent needed for practice.
    agent any

    // WHEN it runs. pollSCM checks GitHub on a schedule.
    // 'H/2 * * * *' means "every 2 minutes". Because Jenkins is on
    // localhost, GitHub cannot push a webhook to it, so Jenkins
    // reaches out and asks "any new commits?" instead.
    triggers {
        pollSCM('H/2 * * * *')
    }

    stages {

        // ---- STAGE 1: CHECKOUT ----
        // Pull the latest code from GitHub onto the Jenkins machine.
        // 'checkout scm' uses the repo you configure in the job.
        stage('Checkout') {
            steps {
                echo 'Pulling latest code from GitHub...'
                checkout scm
            }
        }

        // ---- STAGE 2: CI TEST (the gate) ----
        // Prove index.html is present and valid BEFORE we deploy.
        // If any check fails, the stage fails and the pipeline stops,
        // so a broken page can never reach the deploy stage.
        stage('CI Test') {
            steps {
                echo 'Running CI checks on index.html...'
                // Windows batch commands (bat), because Jenkins is on Windows.
                // 1) the file must exist
                bat 'if not exist index.html exit 1'
                // 2) it must contain a closing </html> tag (a basic validity check)
                bat 'findstr /C:"</html>" index.html >nul || exit 1'
                echo 'CI passed: index.html exists and looks valid.'
            }
        }

        // ---- STAGE 3: DEPLOY ----
        // For local practice, "deploy" = copy the site to a folder
        // that represents the live server. This proves the mechanism
        // without needing a cloud account. Later this same stage would
        // push to Netlify, Azure, an FTP server, etc.
        stage('Deploy') {
            steps {
                echo 'Deploying to the local live folder...'
                bat 'if not exist C:\\cicd-live mkdir C:\\cicd-live'
                bat 'copy /Y index.html C:\\cicd-live\\index.html'
                echo 'Deployed. Open C:\\cicd-live\\index.html in a browser.'
            }
        }
    }

    // ---- POST: runs after all stages, whatever the outcome ----
    post {
        success {
            echo 'PIPELINE PASSED — code was tested and deployed.'
        }
        failure {
            echo 'PIPELINE FAILED — check which stage went red. Nothing deployed.'
        }
    }
}
