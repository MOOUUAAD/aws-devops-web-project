<div align="center">

# 🚀 End-to-End CI/CD Pipeline on AWS 🚀

*Automating the build, test, and deployment of a Java web application on the AWS ecosystem.*

</div>

<!-- Project Status -->
<p align="center">
  <img src="https://img.shields.io/badge/Project_Status-Complete-brightgreen?style=for-the-badge" alt="Project Status: Complete"/>
</p>

<!-- Introduction Card -->
<div align="center">
  <table style="width: 90%; border: 1px solid #444; border-radius: 8px; background-color: #161b22; padding: 15px;">
    <tr>
      <td>
        <h3 style="margin-top: 0;">🎯 Project Overview</h3>
        <p style="margin-bottom: 0;">This project demonstrates the construction of a complete CI/CD pipeline using a suite of powerful AWS services. It showcases a modern, cloud-native approach to DevOps, automating the entire workflow from a <code>git push</code> to a live production deployment.</p>
      </td>
    </tr>
  </table>
</div>


---

## 🏗️ Architecture Diagram

The following diagram illustrates the high-level architecture and the flow of the CI/CD pipeline, from a code commit on GitHub to the final deployment.

<p align="center">
<img width="3420" height="1096" alt="image" src="https://github.com/user-attachments/assets/31cfbfd9-f8b5-4832-97c1-4759714a64db" />

</p>

---

## 🗺️ Project Roadmap

This project is documented in seven distinct phases. Use the links below to navigate to a specific section.

*   [Phase 1: Set Up a Web App in the Cloud](#phase-1)
*   [Phase 2: Artifact Management with AWS CodeArtifact](#phase-2)
*   [Phase 3: Automated Builds with AWS CodeBuild](#phase-3)
*   [Phase 4: Infrastructure as Code with AWS CloudFormation](#phase-4)
*   [Phase 5: Preparing for Deployment with CodeDeploy](#phase-5)
*   [Phase 6: Configuring Automated Deployments with AWS CodeDeploy](#phase-6)
*   [Phase 7: Executing and Verifying the Deployment](#phase-7)
*   [Conclusion & Key Learnings](#conclusion)


---

<details id="phase-1">
<summary>
<h3>🚀 Set Up a Web App in the Cloud</h3>
</summary>

This foundational phase involved provisioning a secure AWS environment, scaffolding a web application, and establishing a version control workflow with Git and GitHub.

#### **Cloud Infrastructure & Security** 🌩️
My key tasks were:
*   **Created a dedicated IAM Admin User** to avoid using the root account for all operations.
*   **Launched a new `t3.micro` EC2 instance** running Amazon Linux 2023 to serve as the application host.
*   **Configured the instance's Security Group** to restrict SSH access strictly to my personal IP address.
*   **Generated and securely stored** a new `.pem` key pair for authenticating my SSH connections.

*My EC2 instance running successfully in the AWS Management Console:*
> <!-- Add screenshot here -->

---
#### **Remote Development & Application Scaffolding** 🛠️
Next, I configured the server and created the initial web application. My key tasks were:
*   **Connected to the EC2 instance remotely** using Visual Studio Code with the Remote-SSH extension.
*   **Installed project dependencies** on the server, including Apache Maven and Amazon Corretto 8 (Java).
*   **Used Maven's `archetype:generate` command** to instantly create the file structure for a standard Java web application.
*   **Modified the `index.jsp` file** to include a personalized welcome message as the first code change.

*VS Code showing the project files being edited directly on the remote EC2 server:*
> <!-- Add screenshot here -->

---
#### **Version Control with Git & GitHub** 🔄
Finally, I established a version control workflow. My key tasks were:
*   **Installed Git** on the EC2 instance.
*   **Initialized a local Git repository** in the project's root directory.
*   **Created a new public repository** on my GitHub account to host the project.
*   **Generated a GitHub Personal Access Token (PAT)** for secure, command-line authentication.
*   **Pushed the entire project** from my EC2 instance to the remote GitHub repository.

*The project code successfully pushed and visible in my new GitHub repository:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-2">
<summary>
<h3>📦 Artifact Management with AWS CodeArtifact </h3>
</summary>

In this phase, I established a secure, centralized artifact repository using AWS CodeArtifact. This ensures that all software packages for my project are managed in a private, reliable location.

#### **CodeArtifact Repository Setup** 🏛️
I started by creating the central hub for my project's dependencies. My key tasks were:
*   **Created a new CodeArtifact repository** named `nextwork-devops-cicd`.
*   **Configured the repository for `Maven` packages**, as this is a Java project.
*   **Enabled `maven-central-store` as a public upstream repository**, allowing CodeArtifact to fetch and cache public packages securely.

*My new CodeArtifact repository, ready to receive packages:*
> <!-- Add screenshot here -->

---
#### **IAM Configuration for Secure Access** 🔐
To allow my EC2 instance to communicate with CodeArtifact securely, I set up the necessary permissions. My key tasks were:
*   **Authored a new IAM policy** (`codeartifact-nextwork-consumer-policy`) granting specific CodeArtifact permissions.
*   **Created a new IAM role** (`ec2-instance-nextwork-cicd`) for the EC2 service.
*   **Attached the custom policy** to this new role.
*   **Assigned the IAM role** directly to my running EC2 instance, giving it permission to interact with CodeArtifact.

*The IAM role successfully attached to my EC2 instance in the AWS Console:*
> <!-- Add screenshot here -->

---
#### **Maven Integration & Project Build** 🔗
Finally, I connected my Maven project to the new repository and confirmed the integration. My key tasks were:
*   **Exported the temporary `CODEARTIFACT_AUTH_TOKEN`** as an environment variable in my EC2 terminal.
*   **Created and configured a `settings.xml` file** in my project's root to point Maven to the new repository.
*   **Successfully executed `mvn compile -s settings.xml`**, which triggered Maven to download dependencies from CodeArtifact.
*   **Verified that packages were uploaded** to the CodeArtifact repository by checking the AWS console.

*The "BUILD SUCCESS" message and the newly populated packages in CodeArtifact:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-3">
<summary>
<h3>⚙️ Automated Builds with AWS CodeBuild </h3>
</summary>

In this phase, I automated the build process for my application. I configured AWS CodeBuild to pull the source code from GitHub, compile it using dependencies from CodeArtifact, and store the resulting artifact in an S3 bucket.

#### **S3 Bucket for Artifacts** 🪣
My first step was creating a durable location to store my build outputs. My key tasks were:
*   **Created a new, private S3 bucket** to serve as a storage location for the build artifacts generated by CodeBuild.

*The new S3 bucket visible in the AWS Console:*
> <!-- Add screenshot here -->

---
#### **CodeBuild Project Configuration** 🏗️
Next, I configured the automated build job itself. My key tasks were:
*   **Set up a new CodeBuild project** and connected it to my GitHub repository.
*   **Configured the build environment** to use an Amazon Linux 2 image with Corretto 8, matching my development environment.
*   **Updated the project's IAM role** by attaching my `codeartifact-nextwork-consumer-policy`, granting it permission to access CodeArtifact.
*   **Pointed the artifact output** to the S3 bucket created in the previous step.

*My CodeBuild project configuration summary:*
> <!-- Add screenshot here -->

---
#### **Defining and Running the Build** 📝
Finally, I told CodeBuild how to build the project and ran the first test. My key tasks were:
*   **Created a `buildspec.yml` file** in my project's root to define the sequence of build commands.
*   **Pushed the `buildspec.yml` file** to my GitHub repository to make it available to CodeBuild.
*   **Manually triggered the first build** from the AWS CodeBuild console to test the entire configuration.
*   **Monitored the build logs** and confirmed that the build succeeded and the final `.war` artifact was uploaded to my S3 bucket.

*The successful build log in CodeBuild and the resulting artifact in S3:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-4">
<summary>
<h3>🏗️ Infrastructure as Code with AWS CloudFormation </h3>
</summary>

In this phase, I provisioned the production environment using Infrastructure as Code (IaC). I used AWS CloudFormation to create a separate, dedicated set of resources for deploying the live application, ensuring a clean separation from the development environment.

#### **Defining Infrastructure with a Template** 📜
I used a pre-written YAML template to define all the necessary resources, making the infrastructure repeatable and predictable. My key tasks were:
*   **Launched a new CloudFormation Stack** named `NextWorkCodeDeployEC2Stack`.
*   **Uploaded the `nextworkwebapp.yaml` template**, which defined a VPC, Subnet, Internet Gateway, Security Group, and an EC2 instance.
*   **Configured the `MyIP` parameter** with my IP address and a `/32` CIDR block to lock down SSH access for maximum security.
*   **Set the stack failure options** to roll back all resources, ensuring any failed deployments would be automatically cleaned up.

*The stack creation parameters in the AWS CloudFormation console:*
> <!-- Add screenshot here -->

---
#### **Monitoring Stack Creation** 🔍
After launching the stack, I monitored its creation to ensure all resources were provisioned correctly. My key tasks were:
*   **Watched the stack creation progress** in the "Events" tab of the CloudFormation console.
*   **Verified all resources** (VPC, EC2 instance, etc.) were successfully created in the "Resources" tab.
*   **Waited for the final stack status** to become `CREATE_COMPLETE`.

*A screenshot of the 'Resources' tab showing all the newly created infrastructure:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-5">
<summary>
<h3>📜 Preparing for Deployment with CodeDeploy </h3>
</summary>

In this phase, I prepared the instruction manual for AWS CodeDeploy. This involved creating a series of scripts to manage the application lifecycle and a manifest file (`appspec.yml`) to orchestrate the deployment process.

#### **Creating Deployment Scripts** ✍️
I wrote three essential shell scripts to control the application on the target server. My key tasks were:
*   **Created `install_dependencies.sh`** to install Tomcat and Apache httpd, and configure Apache as a reverse proxy.
*   **Created `start_server.sh`** to start the Tomcat and Apache services and enable them to run on system boot.
*   **Created `stop_server.sh`** to safely stop the running application services before a new deployment begins.

*The `scripts` folder and its contents in my VS Code explorer:*
> <!-- Add screenshot here -->

---
#### **Defining the Deployment Specification** 📋
I created the core configuration file that tells CodeDeploy exactly what to do. My key tasks were:
*   **Created an `appspec.yml` file** at the root of my project.
*   **Defined the `files` section** to map the built `.war` file from the artifact to the Tomcat webapps directory.
*   **Defined the `hooks` section** to trigger the shell scripts at specific deployment lifecycle events (`ApplicationStop`, `BeforeInstall`, and `ApplicationStart`).
*   **Updated the `buildspec.yml` file's artifact section** to ensure these new scripts and the `appspec.yml` were included in the final build artifact.

*The `appspec.yml` file and the updated `artifacts` section in `buildspec.yml`:*
> <!-- Add screenshot here -->

---
#### **Committing Deployment Files** ✅
Finally, I added these crucial new files to version control to make them available to the CI/CD pipeline. My key tasks were:
*   **Staged and committed** the new `scripts` folder, the `appspec.yml` file, and the changes to `buildspec.yml`.
*   **Pushed the commit** to the `master` branch on my GitHub repository.

*My GitHub repository showing the newly added `scripts` folder and `appspec.yml` file:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-6">
<summary>
<h3>🤖 Configuring Automated Deployments with AWS CodeDeploy</h3>
</summary>

In this phase, I configured AWS CodeDeploy, which automates the process of deploying my application to the production server. This involved creating an application, defining a deployment group, and setting up the necessary permissions.

#### **CodeDeploy Application & Service Role** 📦
First, I created the high-level components needed for CodeDeploy to operate. My key tasks were:
*   **Created a new CodeDeploy Application** named `nextwork-devops-cicd`, setting its compute platform to `EC2/On-premises`.
*   **Created a new IAM Service Role** (`NextWorkCodeDeployRole`) for CodeDeploy, attaching the `AWSCodeDeployRole` managed policy to grant it the necessary permissions to interact with EC2 and other services.

*The newly created CodeDeploy application and IAM role:*
> <!-- Add screenshot here -->

---
#### **Configuring the Deployment Group** 🎯
This is where I defined the specifics of the deployment strategy. My key tasks were:
*   **Created a Deployment Group** named `nextwork-devops-cicd-deploymentgroup`.
*   **Assigned the `NextWorkCodeDeployRole`** to the group as its service role.
*   **Set the deployment type** to `In-place`.
*   **Targeted the production server** by selecting EC2 instances tagged with `role: webserver`, which matched the instance launched by CloudFormation.
*   **Kept the deployment settings** to deploy `AllAtOnce` and disabled load balancing since I am only deploying to a single instance.

*The deployment group configuration showing the correctly matched EC2 instance:*
> <!-- Add screenshot here -->

</details>

---

<details id="phase-7">
<summary>
<h3>✅ Executing and Verifying the Deployment</h3>
</summary>

In this final phase, I put all the pieces together by manually triggering a deployment with AWS CodeDeploy. This involved pointing CodeDeploy to the correct build artifact, monitoring the deployment process, and finally verifying that the web application was successfully running on the production server.

#### **Triggering the Deployment from S3** 🚀
I initiated the deployment process from the CodeDeploy console. My key tasks were:
*   **Re-ran my CodeBuild project** to ensure the build artifact was up-to-date and included the latest deployment scripts.
*   **Created a new deployment** within my `nextwork-devops-cicd-deploymentgroup`.
*   **Specified the Revision location** by pasting the S3 URI of the `.zip` artifact produced by my latest CodeBuild run.

*The "Create deployment" page in CodeDeploy, showing the S3 revision location:*
> <!-- Add screenshot here -->

---
#### **Monitoring Deployment Lifecycle Events** 📊
I tracked the automated deployment as it happened. My key tasks were:
*   **Tracked the deployment progress** in real-time from the CodeDeploy console.
*   **Observed each lifecycle hook** from my `appspec.yml` (`ApplicationStop`, `BeforeInstall`, `ApplicationStart`, etc.) being executed successfully.
*   **Waited for the overall deployment status** to change to `Succeeded`.

*The deployment details page showing the successful lifecycle events:*
> <!-- Add screenshot here -->

---
#### **Accessing the Live Application** 🌐
The moment of truth! I verified that the deployment was a success. My key tasks were:
*   **Navigated to the target EC2 instance** from the deployment details page.
*   **Copied the instance's Public IPv4 DNS** address.
*   **Pasted the address into a web browser** and confirmed that my web application was live and accessible to the world.

*A screenshot of my live web application running in the browser!*
> <!-- Add screenshot here -->

</details>

---

<div id="conclusion">

## 🎓 Conclusion & Key Learnings

This project was a comprehensive journey through the creation of a modern, automated CI/CD pipeline on AWS. By integrating multiple services, I successfully transformed a manual, multi-step process into a streamlined workflow that starts with a simple `git push`.

Key takeaways from this project include:
*   **End-to-End Cloud Automation**: I successfully orchestrated a full CI/CD pipeline using a suite of interconnected AWS services (CodeArtifact, CodeBuild, CodeDeploy, CloudFormation), demonstrating the power of a fully cloud-native approach.
*   **Infrastructure as Code (IaC)**: Using AWS CloudFormation to define and provision the entire production environment reinforced the importance of creating repeatable, predictable, and version-controlled infrastructure.
*   **Configuration as Code**: Defining the build and deployment logic in `buildspec.yml` and `appspec.yml` files, which live alongside the application code, is a core tenet of modern DevOps that I successfully implemented.
*   **Secure Dependency Management**: I learned how to use AWS CodeArtifact to create a private, secure repository for Maven dependencies, preventing reliance on public repositories and enhancing security.
*   **The Importance of IAM**: This project highlighted the critical role of IAM policies and roles in securely connecting different services, ensuring each component has only the permissions it needs to function.

Ultimately, this project provided invaluable hands-on experience in building the kind of automated, secure, and efficient systems that power modern software development.

</div>
