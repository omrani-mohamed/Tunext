pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = 'my-github-pat'
        GITHUB_REPO = 'https://github.com/omrani-mohamed/Tunext.git'
    }

    triggers {
        //Poll GitHub every 5 minutes
        //pollSCM('H/5 * * * *')

        //Webhook trigger from GitHub
	    githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: "${GITHUB_REPO}"
            }
        }

        stage('Deploy to GitHub Pages') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${GITHUB_CREDENTIALS}", usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh '''
                        # Clone gh-pages branch (or create it if it doesn’t exist yet)
                        if git ls-remote --exit-code --heads ${GITHUB_REPO} gh-pages; then
                            git clone --branch gh-pages ${GITHUB_REPO} gh-pages
                        else
                            git clone ${GITHUB_REPO} gh-pages
                            cd gh-pages
                            git checkout --orphan gh-pages
                            git rm -rf .

                            # Configure Git inside gh-pages repo
                            git config user.name "omrani-mohamed"
                            git config user.email "omrani.mohamedamine@esprit.tn"

                            echo "<h1>GitHub Pages branch initialized</h1>" > index.html
                            git add .
                            git commit -m "Initialize gh-pages branch"
                            git push https://$GIT_USER:$GIT_PASS@github.com/omrani-mohamed/Tunext.git gh-pages
                            cd ..
                        fi

                        cd gh-pages

                        # Configure Git inside gh-pages repo (needed for deploy step too)
                        git config --global user.name "omrani-mohamed"
                        git config --global user.email "omrani.mohamedamine@esprit.tn"

                        # Remove old files
                        rm -rf *

                        # Copy site files, excluding Jenkinsfile and hidden files
                        rsync -av --exclude='.git' --exclude='Jenkinsfile' --exclude='.github' ../ ./

                        # Commit & push
                        git add .
                        git commit -m "Deploy from Jenkins [ci skip]" || echo "No changes to commit"
                        git push https://$GIT_USER:$GIT_PASS@github.com/omrani-mohamed/Tunext.git gh-pages
                    '''
                }
            }
        }
    }
}
