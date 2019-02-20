# sfdx-gitlab-package

For a fully guided walkthrough of setting up and configuring continuous integration using scratch orgs and Salesforce CLI, see the [Continuous Integration Using Salesforce DX](https://trailhead.salesforce.com/modules/sfdx_travis_ci) Trailhead module.

This repository shows one way you can successfully use scratch orgs to create new package versions with GitLab CI/CD. We make a few assumptions in this README. Continue only if you have completed these critical configuration prerequisites.

- You know how to set up your GitLab repository with GitLab CI/CD. (Need help? See the GitLab [Getting Started guide](https://docs.gitlab.com/ee/ci/README.html).)
- You have properly set up the JWT-based authorization flow (headless). We recommended using [these steps for generating your self-signed SSL certificate](https://devcenter.heroku.com/articles/ssl-certificate-self). 

## Getting Started

1) Make sure that you have Salesforce CLI installed. Run `sfdx force --help` and confirm you see the command output. If you don't have it installed, you can download and install it from [here](https://developer.salesforce.com/tools/sfdxcli).

2) [Mirror](https://docs.gitlab.com/ee/workflow/repository_mirroring.html) this repo in to your GitLab account.

3) Clone your mirrored repo locally: `git clone https://gitlab.com/<gitlab_username>/sfdx-gitlab-package.git`

4) Set up a JWT-based auth flow for the target orgs that you want to deploy to. This step creates a `server.key` file that is used in subsequent steps.
(https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm)

5) Confirm that you can perform a JWT-based auth: `sfdx force:auth:jwt:grant --clientid <your_consumer_key> --jwtkeyfile server.key --username <your_username> --setdefaultdevhubusername`

    **Note:** For more info on setting up JWT-based auth, see [Authorize an Org Using the JWT-Based Flow](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_jwt_flow.htm) in the [Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev).

6) From your JWT-based connected app on Salesforce, retrieve the generated `Consumer Key`.

7) Set up GitLab CI/CD [environment variables](https://gitlab.com/help/ci/variables/README#variables) for your Salesforce `Consumer Key` and `Username`. Note that this username is the username that you use to access your Salesforce org.

    Create an environment variable named `SF_CONSUMER_KEY` and set it as protected.

    Create an environment variable named `SF_USERNAME` and set it as protected.

    **Note:** Setting the variables as protected requires that you set the branch to protected as well.

8) Encrypt the generated `server.key` file and add the encrypted file (`server.key.enc`) to the folder named `assets`.

    `openssl aes-256-cbc -salt -e -in server.key -out server.key.enc -k password`

9) Set up GitLab CI/CD [environment variable](https://gitlab.com/help/ci/variables/README#variables) for the password you used to encrypt your `server.key` file.

    Create an environment variable named `SERVER_KEY_PASSWORD` and set it as protected.

10) Copy all the contents of `package-sfdx-project.json` into `sfdx-project.json` and save.

11) Create the sample package.

    `sfdx force:package:create --path force-app/main/default/ --name "GitLab CI" --description "GitLab CI Package Example" --packagetype Unlocked`

12) Create the first package version.

    `sfdx force:package:version:create --package "GitLab CI" --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg`

13) In the `.gitlab-ci.yml` file, update the value in the `PACKAGENAME` variable to be Package ID in your `sfdx-project.json` file.  This ID starts with `0Ho`.

14) Commit the updated `sfdx-project.json` and `.gitlab-ci.yml` files.

Now you're ready to go! When you commit and push a change, your change kicks off a GitLab CI build.

Enjoy!

## Contributing to the Repository ###

If you find any issues or opportunities for improving this repository, fix them! Feel free to contribute to this project by [forking](http://help.github.com/fork-a-repo/) this repository and making changes to the content. Once you've made your changes, share them back with the community by sending a pull request. Please see [How to send pull requests](http://help.github.com/send-pull-requests/) for more information about contributing to GitHub projects.

## Reporting Issues ###

If you find any issues with this demo that you can't fix, feel free to report them in the [issues](https://github.com/forcedotcom/sfdx-gitlab-package/issues) section of this repository.
