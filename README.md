# Code Scanning Java Tutorial

Welcome to the Code Scanning Java Tutorial! This tutorial will take you through how to set up Github Advanced Security: Code Scanning as well as interpret results that it may find. The following repository contains SQL injection vulnerability for demonstration purpose.

## Introduction

Code scanning is a feature that you use to analyze the code in a GitHub repository to find security vulnerabilities and coding errors. Any problems identified by the analysis are shown in GitHub.

You can use code scanning with CodeQL, a semantic code analysis engine. CodeQL treats code as data, allowing you to find potential vulnerabilities in your code with greater confidence than traditional static analyzers.

This tutorial with use CodeQL Analysis with Code Scanning in order to search for vulnerabilities within your code. 

## Instructions

<details>
<summary>Create repository fork</summary>
<p> 
  
Begin by [creating a new repository from a fork (public)](https://docs.github.com/en/get-started/quickstart/fork-a-repo) or [cloning the repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository).

<img src="images/00-repo-fork.png" width="70%"/>

Where creating the forked repository, make sure to 

1. Select the correct org / user account
2. Create a name for your new repository
3. Disable main branch only cloning
4. Create the repository from the template

</p>
</details>

<details>
<summary>Enable Code Scanning</summary>
<p> 

#### Security tab

Click on the `Security` tab.


<img src="images/00-repo-security-tab.png" width="70%"/>

#### Set up code scanning

Click `Set up code scanning`.

<img src="images/01-repo-secruity-setup-code-scanning.png" width="70%"/>

#### Setup Workflow

Click the `Setup` dropdown and select the Default CodeQL Analysis.

![image](https://github.com/user-attachments/assets/294a1d2a-b58a-4874-bced-c22a76fe315a)

This will trigger a CodeQL Scan without needing a workflow file. Since Java is a compiled language the file will use our out-of-the-box [Autobuild action](https://docs.github.com/en/code-security/code-scanning/creating-an-advanced-setup-for-code-scanning/codeql-code-scanning-for-compiled-languages) but if your application requires more customizable compilation steps, you can switch to the advanced setup and create a workflow file where you can input your desired steps. See the [documentation](https://docs.github.com/en/free-pro-team@latest/github/finding-security-vulnerabilities-and-errors-in-your-code/running-codeql-code-scanning-in-your-ci-system) if you would like to configure CodeQL Analysis with a 3rd party CI system instead of using GitHub Actions.
</p>
</details>

<details>
  
<summary>Actions Workflow file (No need to do anything!) </summary>
<p>

#### Actions Workflow

As we're going with the Default Setup, this file is not necessary but in case you're curious, here how it looks like:

The Actions Workflow file contains a number of different sections including:
1. Checking out the repository
2. Initializing the CodeQL Action
3. Running Autobuilder (or code your own build steps if autobuild doesn't work)
4. Running the CodeQL Analysis

<img src="images/03-actions-sample-workflow.png" width="80%"/>

Click `Start Commit` -> `Commit this file` to commit the changes to _main_ branch.
</p>
</details>

<details>
  
<summary>Workflow triggers</summary>
<p>

#### Workflow triggers

There are a [number of events](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows) that can trigger a GitHub Actions workflow. 

In this example, with the default setup the triggers will be:
![image](https://github.com/user-attachments/assets/6bcc8f35-8f04-45e3-aa1f-82fce86d60ae)

Whereas with the workflow, it will be triggered on:

<img src="images/04-actions-sample-events.png" width="50%"/>

- push to _main_ branch
- pull request to merge to _main_ branch
- on schedule, at 6:33 every Thursday

Setting up the new CodeQL workflow and committing it to _main_ branch in the step above will trigger the scan.

</p>
</details>


<details>
<summary>GitHub Actions Progress</summary>

<p>
 
#### GitHub Actions Progress

Click `Actions` tab -> `CodeQL`

Click the specific workflow run. You can view the progress of the Workflow run until the analysis completes.

<img src="images/05-actions-completed.png" width="80%"/>

</p>
</details>

<details>
<summary>Security Issues</summary>
<p>
  
Once the Workflow has completed, click the `Security` tab -> ` Code Scanning Alerts`. An security alert "Query built from user-controlled sources" should be visible.

#### Security Alert View

Clicking on the security alert will provide details about the security alert including: <br/>
<ul>
<li>A description of the issue </li>
<li>A tag to the CWE that it is connected to as well as the type of alert (Error, Warning, Note)</li>
<li>The line of code that triggered the security alert</li>
<li>The ability to dismiss the alert depending on certain conditions (`False positive`? `Won't fix`? `Used in tests`?)</li>
</ul>
<img src="images/06-security-codeql-alert.png" width="80%"/>

#### Security Alert Description

Click `Show more` to view a full desciption of the alert including examples and links to additional information.

<img src="images/07-security-codeql-show-more.png" width="80%"/>

#### Security Full Description

<img width="80%" src="images/08-security-codeql-full-desc.png">

</p>
</details>

<details>
<summary>Show Paths</summary>
<p>

#### Show Paths Button

CodeQL Analysis is able to trace the dataflow path from source to sink and gives you the ability to view the path traversal within the alert.

Click `show paths` in order to see the dataflow path that resulted in this alert.

<img src="images/09-security-codeql-show-paths.png" width="80%"/>

#### Show Paths View

<img src="images/10-security-codeql-show-paths-details.png" width="80%"/>

</p>
</details>

<details>
<p>  
<summary>Fix the Security Alert (with Copilot)</summary> 
<p>
In order to fix this specific alert, we will need to ensure parameters used in the SQL query is validated and sanitized. We will solve this with the power of Copilot!
</p>
Open the file [`IndexController.java`](./src/main/java/com/github/hackathon/advancedsecurityjava/Controllers/IndexController.java) in the `Controllers` folder and select line 40. Once highlighted, select `Shift` on your keyboard and click line 53. Finally, click on the Copilot icon that appears to the side of the highlighted code. 
  
![image](https://github.com/user-attachments/assets/2251deb3-2498-4f2f-a355-e35b37de58a4)

Ask Copilot the following prompt or feel free to try with a prompt of your own!
- English: Rewrite this method to prevent a SQL injection
- Spanish: Reescribe este método para prevenir SQL injection

Integrate the suggested code in your Index Controller. Make sure to click [Edit](https://docs.github.com/en/free-pro-team@latest/github/managing-files-in-a-repository/editing-files-in-your-repository) on the file. 

Click `Create a new branch for this commit and start a pull request`, name the branch `fix-sql-injection`, and create the Pull Request.

</details>
<details>
<p>
<summary>Fix the Security Alert (without Copilot)</summary>
</p>
In order to fix this specific alert, we will need to ensure parameters used in the SQL query is validated and sanitized.

Click on the `Code` tab and [Edit](https://docs.github.com/en/free-pro-team@latest/github/managing-files-in-a-repository/editing-files-in-your-repository) the file [`IndexController.java`](./src/main/java/com/github/hackathon/advancedsecurityjava/Controllers/IndexController.java) in the `Controllers` folder, replace the content with the file [`fixme`](./fixme).

<img src="images/11-fix-source-code.png" width="30%"/>

Click `Create a new branch for this commit and start a pull request`, name the branch `fix-sql-injection`, and create the Pull Request.

</details>
<details>
<p>
<summary>Re-Scan your code after new changes</summary>
</p>
  
#### Pull Request Status Check

In the Pull Request, you will notice that the CodeQL Analysis has started as a status check. Wait until it completes.

<img src="images/12-fix-pr-in-progress.png" width="80%"/>

#### Security Alert Details

After the Workflow has completed click on `Details` by the `Code Scanning Results / CodeQL` status check. 

<img src="images/13-fix-pr-done.png" width="80%"/>

#### Fixed Alert

Notice that Code Scanning has detected that this Pull Request will fix the SQL injection vulnerability that was detected before.

<img src="images/14-fix-detail.png" width="80%"/>

Merge the Pull Request. After the Pull Request has been merged, another Workflow will kick off to scan the repository for any vulnerabilties. 

#### Closed Security Alerts

After the final Workflow has completed, navigate back to the `Security` tab and click `Closed`. Notice that the **Query built from user-controlled sources** security alert now shows up as a closed issue.

<img src="images/15-fixed-alert.png" width="80%"/>

#### Traceability

Click on the security alert and notice that it details when the fix was made, by whom, and the specific commit. This provides full traceability to detail when and how a security alert was fixed and exactly what was changed to remediate the issue.

<img src="images/16-fix-history.png" width="80%"/>

</p>
</details>

<details>
<summary>Prevent new Alerts in a Pull Request</summary>
<p>

#### Create Pull Request from new feature Branch

Now that we have setup CodeQL Analysis and have fix a security alert, we can try to introduce an alert into a Pull Request.

Create a new Pull Request with the base branch as your `main` branch and the compare branch as the `new-feature` branch.

<img src="images/17-create-pull-request.png" width="70%"/>

Make sure that the base branch is set to your own repositories `main` branch versus the original repository's `main` branch.


#### Pull Request Status Check

Once the Pull Request has been created, you will notice that the CodeQL Analysis has started as a status check. Wait until it completes.

After the Workflow has completed, the `Code Scanning Results / CodeQL` status check will have failed.
Notice that Code Scanning has detected that this Pull Request introduces a new security alert.

<img src="images/18-pr-check-failed.png" width="80%"/>


#### Alert Centric Notifications

Directly in the Pull Request, you will notice that GitHub Code Scanning bot has left a review of the Pull Request with the security alert details.
This will help developers to quickly identify security issues introduced in their Pull Requests.

<img src="images/19-pr-review.png" width="80%"/>


This also allows for collaboration between developers and security teams to discuss the security alert and how to remediate it.

<img src="images/20-pr-review-collaboration.png" width="80%"/>

#### Security Alert Details

Click on `Show more details` by the new `Code Scanning Alert` to jump to the `Security` tab and view the security alert details.

<img src="images/21-pr-show-more-details.png" width="80%"/>

Notice that the security alert was found `In pull request` and not in the `main` branch (production).


</p>
</details>
<details>
<p><summary>(Bonus) Enable Secret Scanning</summary></p>
<p>
Click on `Settings` and select `Code Security` from the menu on the left.
  
![image](https://github.com/user-attachments/assets/d56f94fb-5623-481f-b850-291248104304)

Find the options for Secret Scanning and Push Protection and make sure they are both Enabled. When they are it should say disabled, similar to this image:

![image](https://github.com/user-attachments/assets/23f92127-bef0-4107-91e2-00a802daff47)

</p>
</details>
<details>
<p><summary>(Bonus) Try to introduce a Secret</summary></p>
<p>

Find yourself a secret that matches any of the [supported secrets](https://docs.github.com/en/code-security/secret-scanning/introduction/supported-secret-scanning-patterns#supported-secrets) and try to commit the value to the Repo. You could commit it to any file! Refer to the previous activities to refresh how Edit and Commit a change. 

If you received an error, you've done it right!!

If you didn't receive an error, this may be why:
1. The secret is not supported out-of-the-box. For secrets not in the [supported secrets](https://docs.github.com/en/code-security/secret-scanning/introduction/supported-secret-scanning-patterns#supported-secrets) list, you will need to [create a Custom Pattern](https://docs.github.com/en/enterprise-cloud@latest/code-security/secret-scanning/using-advanced-secret-scanning-and-push-protection-features/custom-patterns/defining-custom-patterns-for-secret-scanning) first.
2. The secret was already leaked and you already have an Open Alert in your Security page. Why cry over spilled milk?!
3. Check again that Push Protection is enabled on your Repo!

</p>
</details>


## Next Steps

Ready to talk about advanced security features for GitHub Enterprise? [Contact Sales](https://enterprise.github.com/contact) for more information!

Check out [GitHub's Security feature page](https://github.com/features/security) for more security features embedded into GitHub.

Check out the Code Scanning [documentation](https://docs.github.com/en/free-pro-team@latest/github/finding-security-vulnerabilities-and-errors-in-your-code/about-code-scanning) for additional configuration options and technical details.

clave=f0FArCXS7Jtub12345ABCDEfghijk87654321
