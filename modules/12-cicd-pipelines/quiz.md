# Module 12: Quiz

1. Which of the following best describes the difference between Continuous Integration (CI) and Continuous Delivery (CD)?

   A) CI automates deployments to production, while CD automates building and testing code
   B) CI automatically builds and tests code every time a developer pushes changes to a shared repository, while CD extends CI by automatically deploying validated code to staging or production environments
   C) CI and CD are the same practice with different names used by different cloud providers
   D) CI requires manual approval for every code change, while CD removes all human involvement from the release process

2. A developer is writing a `buildspec.yml` file for an AWS CodeBuild project. The file needs to install dependencies, run linting and unit tests, compile the application, and log a completion message. Which of the following correctly maps each task to the appropriate buildspec phase?

   | Task | Phase |
   |------|-------|
   | Install npm dependencies | _______ |
   | Run linting and unit tests | _______ |
   | Compile the application with `npm run build` | _______ |
   | Log a completion timestamp | _______ |

   Options:
   A) `install`, `pre_build`, `build`, `post_build`
   B) `pre_build`, `build`, `post_build`, `install`
   C) `install`, `build`, `pre_build`, `post_build`
   D) `build`, `pre_build`, `install`, `post_build`

3. In an AWS CodeDeploy `appspec.yml` file for an EC2/on-premises deployment, which lifecycle hook should you use to run a script that verifies the application is responding to HTTP requests after the deployment completes?

   A) `BeforeInstall`
   B) `AfterInstall`
   C) `ApplicationStart`
   D) `ValidateService`

4. True or False: In AWS CodePipeline, stages within a pipeline run in parallel by default, while actions within a single stage run sequentially.

5. A team is deploying a critical e-commerce application that processes thousands of orders per minute. They need a deployment strategy that provides near-zero downtime and the ability to roll back to the previous version within seconds if the new version has a defect. Which deployment strategy best meets these requirements?

   A) All-at-once, because it is the fastest deployment method
   B) Rolling, because it updates instances in batches and maintains partial availability
   C) Blue/green, because it maintains the old environment intact and can redirect traffic back instantly if the new environment fails health checks
   D) Canary, because it tests with a small percentage of traffic first

6. Which of the following is a key advantage of using a manual approval action in an AWS CodePipeline production pipeline?

   A) It eliminates the need for automated testing by relying on human review instead
   B) It pauses the pipeline and allows a human reviewer to verify the staging deployment, review changes, and approve or reject the release before it reaches production
   C) It automatically rolls back the deployment if no one approves within 24 hours
   D) It sends the deployment artifacts directly to the reviewer's local machine for testing

7. A development team uses GitHub as their source control platform and needs to deploy their application to Amazon ECS using blue/green deployments managed by AWS CodeDeploy. They want pull request builds and tests to run in GitHub, but production deployments must use the AWS deployment toolchain. Which approach best fits these requirements?

   A) Use CodePipeline for everything, including pull request builds
   B) Use GitHub Actions for everything, including ECS blue/green deployments
   C) Use GitHub Actions for CI (build and test on pull requests) and CodePipeline for CD (deploy to AWS environments after merging to main), combining the developer experience of GitHub with the deployment capabilities of AWS
   D) Use Jenkins as a standalone CI/CD tool to avoid choosing between GitHub Actions and CodePipeline

8. How does AWS CodePipeline pass data between stages in a pipeline?

   A) Each stage reads directly from the source repository to get the latest code
   B) Stages communicate through named artifacts stored as ZIP files in an Amazon S3 bucket, where each stage produces output artifacts that subsequent stages consume as input artifacts
   C) Stages share data through environment variables that persist across the entire pipeline execution
   D) CodePipeline uses Amazon DynamoDB to store intermediate build results between stages

9. Your team has a CodePipeline that deploys a web application to Amazon EC2 instances using CodeDeploy with the `AllAtOnce` deployment configuration. During a recent deployment, a bug in the new version caused all instances to return HTTP 500 errors simultaneously, resulting in a 15-minute outage while the team manually redeployed the previous version. The team wants to prevent this from happening again while keeping deployment times reasonable. Which combination of changes would most effectively reduce the risk of a full outage during future deployments?

   A) Switch to the `OneAtATime` deployment configuration and remove all automated testing from the pipeline
   B) Switch to a blue/green deployment strategy so the old environment remains available for instant rollback, and configure CodeDeploy automatic rollback triggered by a CloudWatch alarm on the HTTP 5xx error rate
   C) Keep the `AllAtOnce` configuration but add a manual approval action after deployment
   D) Switch to a canary deployment and remove the staging environment to save costs

10. A team stores their pipeline definition as a CloudFormation template in version control alongside their application code. Which of the following is NOT a benefit of defining the pipeline as code?

    A) Pipeline changes can be reviewed through pull requests before being applied
    B) The pipeline can be recreated in another AWS account or Region using the same template
    C) The pipeline definition is automatically encrypted at rest by CloudFormation, eliminating the need for IAM roles
    D) The pipeline configuration history is tracked in version control, providing an audit trail of changes

---

<details>
<summary>Answer Key</summary>

1. **B) CI automatically builds and tests code every time a developer pushes changes to a shared repository, while CD extends CI by automatically deploying validated code to staging or production environments**
   Continuous Integration focuses on automatically building and testing code on every commit to catch integration errors early. Continuous Delivery extends this by automating the deployment of validated code to staging or production, with an optional manual approval gate before production. CI and CD are independent but complementary practices. A team can adopt CI without CD by automating builds and tests while still deploying manually.
   Further reading: [What is AWS CodePipeline?](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)

2. **A) `install`, `pre_build`, `build`, `post_build`**
   The buildspec.yml file defines four sequential phases. The `install` phase is for installing dependencies and selecting runtime versions (`npm ci`, `pip install`). The `pre_build` phase runs tasks that must complete before the main build, such as linting and unit tests. The `build` phase performs the main compilation or packaging step. The `post_build` phase runs after the build completes, typically for logging, notifications, or pushing Docker images. If any command in a phase fails, CodeBuild marks the build as failed and skips remaining phases.
   Further reading: [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)

3. **D) ValidateService**
   The `ValidateService` lifecycle hook runs after the application has been installed and started. It is specifically designed for running validation scripts that verify the deployment was successful, such as checking that the application responds to HTTP requests, verifying health check endpoints, or running smoke tests. `BeforeInstall` runs before files are copied. `AfterInstall` runs after files are copied but before the application starts. `ApplicationStart` runs the commands to start the application.
   Further reading: [AppSpec 'hooks' section](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)

4. **False.**
   In AWS CodePipeline, stages run sequentially (the deploy stage waits for the build stage to complete). Actions within a single stage can be configured to run either sequentially or in parallel, depending on their `runOrder` setting. Actions with the same `runOrder` value run in parallel, while actions with different values run in the order specified.
   Further reading: [CodePipeline concepts](https://docs.aws.amazon.com/codepipeline/latest/userguide/concepts.html)

5. **C) Blue/green, because it maintains the old environment intact and can redirect traffic back instantly if the new environment fails health checks**
   Blue/green deployment creates a complete copy of the production environment (green) running the new version alongside the existing environment (blue). After the green environment passes health checks, traffic shifts from blue to green. If the green environment fails, traffic redirects back to blue immediately, providing near-zero downtime and instant rollback. All-at-once (A) would affect all instances simultaneously with no fallback. Rolling (B) maintains partial availability but rollback takes longer. Canary (D) is also viable but the question emphasizes instant rollback for the entire application, which blue/green provides most directly.
   Further reading: [Blue/green deployments](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/bluegreen-deployments.html)

6. **B) It pauses the pipeline and allows a human reviewer to verify the staging deployment, review changes, and approve or reject the release before it reaches production**
   A manual approval action pauses the pipeline execution and sends a notification (typically through Amazon SNS) to designated reviewers. The reviewer can inspect the staging deployment, review the change log, and either approve (allowing the pipeline to continue to production) or reject (stopping the pipeline). Manual approvals do not replace automated testing (A is incorrect). The default timeout is 7 days, not 24 hours (C is incorrect). Artifacts are not sent to the reviewer's machine (D is incorrect).
   Further reading: [Manual approval actions in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html)

7. **C) Use GitHub Actions for CI (build and test on pull requests) and CodePipeline for CD (deploy to AWS environments after merging to main), combining the developer experience of GitHub with the deployment capabilities of AWS**
   This hybrid approach leverages the strengths of both platforms. GitHub Actions provides tight integration with GitHub for pull request workflows, build and test automation, and the large ecosystem of community actions. CodePipeline provides native integration with AWS deployment services like CodeDeploy for ECS blue/green deployments, manual approval gates with SNS notifications, and pipeline visualization in the AWS Console. CodePipeline does not natively support pull request builds (A is incorrect). GitHub Actions does not have built-in support for CodeDeploy ECS blue/green deployments (B is incorrect).
   Further reading: [What is AWS CodePipeline?](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)

8. **B) Stages communicate through named artifacts stored as ZIP files in an Amazon S3 bucket, where each stage produces output artifacts that subsequent stages consume as input artifacts**
   CodePipeline uses an S3 bucket (created automatically or specified by the user) as the artifact store. Each action can produce output artifacts and consume input artifacts. For example, the source action produces a source artifact containing the repository code. The build action consumes the source artifact and produces a build artifact containing compiled output. The deploy action consumes the build artifact. Artifacts are referenced by name in the pipeline configuration and in the buildspec.yml file.
   Further reading: [Input and output artifacts](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome-introducing-artifacts.html)

9. **B) Switch to a blue/green deployment strategy so the old environment remains available for instant rollback, and configure CodeDeploy automatic rollback triggered by a CloudWatch alarm on the HTTP 5xx error rate**
   This combination addresses both the root cause (all instances updated simultaneously with no fallback) and the slow recovery (manual redeployment). Blue/green deployment keeps the old environment running during the deployment, so traffic can be redirected back instantly if the new version fails. Configuring automatic rollback with a CloudWatch alarm on the 5xx error rate ensures that CodeDeploy detects the problem and rolls back without waiting for human intervention. Removing automated testing (A) would increase risk. Adding a manual approval after deployment (C) does not prevent the outage since all instances are already affected. Removing the staging environment (D) would reduce the team's ability to catch bugs before production.
   Further reading: [Redeploy and roll back a deployment with CodeDeploy](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-rollback-and-redeploy.html)

10. **C) The pipeline definition is automatically encrypted at rest by CloudFormation, eliminating the need for IAM roles**
    Defining a pipeline as code (in CloudFormation or CDK) provides version-controlled change history, the ability to recreate the pipeline in other accounts or Regions, and pull request reviews for pipeline changes. However, CloudFormation does not eliminate the need for IAM roles. The pipeline still requires a service role with appropriate permissions to access S3 artifacts, start CodeBuild projects, and trigger deployments. Encryption at rest for the template itself is an S3 feature (if stored there), not a CloudFormation feature that replaces IAM.
    Further reading: [CodePipeline pipeline structure reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
