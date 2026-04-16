# Module 12: Resources

## Official Documentation

### CI/CD Fundamentals and AWS Developer Tools

- [What is AWS CodePipeline?](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome.html)
- [CodePipeline Concepts (Stages, Actions, Artifacts, Transitions)](https://docs.aws.amazon.com/codepipeline/latest/userguide/concepts.html)
- [What is AWS CodeBuild?](https://docs.aws.amazon.com/codebuild/latest/userguide/welcome.html)
- [What is AWS CodeDeploy?](https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html)
- [What is AWS CodeArtifact?](https://docs.aws.amazon.com/codeartifact/latest/ug/welcome.html)
- [What is AWS CodeCommit?](https://docs.aws.amazon.com/codecommit/latest/userguide/welcome.html)

### AWS CodeBuild

- [Build Specification Reference for CodeBuild (buildspec.yml)](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)
- [Build Phase Transitions](https://docs.aws.amazon.com/codebuild/latest/userguide/view-build-details-phases.html)
- [AWS CodeBuild Concepts](https://docs.aws.amazon.com/codebuild/latest/userguide/concepts.html)
- [Build Environment Reference (Available Images)](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-available.html)
- [Monitoring Builds in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/monitoring-builds.html)
- [Runtime Versions in Buildspec File Sample](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-runtime-versions.html)
- [Use CodeBuild with Serverless Applications](https://docs.aws.amazon.com/codebuild/latest/userguide/serverless-applications.html)
- [Troubleshooting AWS CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html)

### AWS CodeDeploy

- [CodeDeploy AppSpec File Reference](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html)
- [AppSpec File Examples](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-example.html)
- [AppSpec 'hooks' Section (Lifecycle Events)](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)
- [Validate Your AppSpec File](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-validate.html)
- [Working with Deployment Configurations](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html)
- [Working with Deployment Groups](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html)
- [Redeploy and Roll Back a Deployment](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-rollback-and-redeploy.html)
- [Troubleshoot EC2/On-Premises Deployment Issues](https://docs.aws.amazon.com/codedeploy/latest/userguide/troubleshooting-deployments.html)

### AWS CodePipeline

- [CodePipeline Pipeline Structure Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html)
- [Input and Output Artifacts](https://docs.aws.amazon.com/codepipeline/latest/userguide/welcome-introducing-artifacts.html)
- [Valid Input and Output Artifacts for Each Action Type](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-action-artifacts.html)
- [Valid Action Providers in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/actions-valid-providers.html)
- [Working with Stage Transitions](https://docs.aws.amazon.com/codepipeline/latest/userguide/transitions.html)
- [Manual Approval Actions in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals.html)
- [Add a Manual Approval Action to a Pipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals-action-add.html)
- [Grant Approval Permissions to an IAM User](https://docs.aws.amazon.com/codepipeline/latest/userguide/approvals-iam-permissions.html)
- [Monitoring CodePipeline Events](https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html)
- [Configure a Stage for Automatic Rollback](https://docs.aws.amazon.com/codepipeline/latest/userguide/stage-rollback-auto.html)
- [Troubleshooting CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/troubleshooting.html)

### CodePipeline Action References (Used in Lab)

- [S3 Source Action Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-S3.html)
- [CodeBuild Action Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CodeBuild.html)
- [CloudFormation Action Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CloudFormation.html)

### AWS CodeArtifact

- [What is AWS CodeArtifact?](https://docs.aws.amazon.com/codeartifact/latest/ug/welcome.html)
- [Connect a CodeArtifact Repository to a Public Repository](https://docs.aws.amazon.com/codeartifact/latest/ug/external-connection.html)
- [Repository Policies](https://docs.aws.amazon.com/codeartifact/latest/ug/repo-policies.html)
- [Use CodeArtifact with mvn](https://docs.aws.amazon.com/codeartifact/latest/ug/maven-mvn.html)

### Deployment Strategies

- [Overview of Deployment Options on AWS (Whitepaper)](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/welcome.html)
- [Blue/Green Deployments](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/bluegreen-deployments.html)
- [Rolling Deployments](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/rolling-deployments.html)
- [Canary Deployments](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/canary-deployments.html)

### GitHub Actions and OIDC Federation

- [Creating OpenID Connect (OIDC) Identity Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html)

### SAM CLI (Used in Lab)

- [sam init](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-init.html)
- [sam build](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-build.html)
- [sam deploy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-deploy.html)

## AWS Whitepapers

- [Overview of Deployment Options on AWS](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/welcome.html): Covers deployment strategies available on AWS, including all-at-once, rolling, blue/green, and canary deployments, with guidance on when to use each approach.
- [Introduction to DevOps on AWS](https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/introduction-to-devops.html): Covers CI/CD practices, infrastructure as code, monitoring, and deployment strategies in the context of AWS services and DevOps culture.
- [Deployment Strategies (Introduction to DevOps on AWS)](https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/deployment-strategies.html): Discusses how organizations choose deployment strategies based on their business model, risk tolerance, and release cadence.

## AWS FAQs

- [AWS CodePipeline FAQ](https://aws.amazon.com/codepipeline/faqs/): Covers CodePipeline pricing (per active pipeline per month), supported source providers, integration with CodeBuild and CodeDeploy, artifact management, and pipeline execution behavior.
- [AWS CodeBuild FAQ](https://aws.amazon.com/codebuild/faqs/): Covers CodeBuild pricing (per build minute), supported build environments, buildspec configuration, caching, and integration with CodePipeline and other AWS services.
- [AWS CodeDeploy FAQ](https://aws.amazon.com/codedeploy/faqs/): Covers CodeDeploy pricing (free for EC2 deployments), supported compute platforms, deployment configurations, rollback behavior, and integration with Auto Scaling and load balancers.

## AWS Architecture References

- [Overview of Deployment Options on AWS](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/welcome.html): Comprehensive reference covering deployment services, strategies, and patterns available on AWS, including detailed comparisons of blue/green, rolling, and canary approaches.
- [CodePipeline Pipeline Structure Reference](https://docs.aws.amazon.com/codepipeline/latest/userguide/reference-pipeline-structure.html): Detailed reference for the JSON structure of a CodePipeline pipeline definition, including stages, actions, artifact stores, and action type configurations. Essential for defining pipelines as code.
- [Invoke a Lambda Function in a Pipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/actions-invoke-lambda-function.html): Reference architecture for extending CodePipeline with custom Lambda functions for tasks such as creating resources, running custom tests, or integrating with third-party services.
