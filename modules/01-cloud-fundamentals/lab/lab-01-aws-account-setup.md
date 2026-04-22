# Lab 01: AWS Account Setup and Console Tour

## Objective

Create and secure an AWS account, set up an IAM administrative user, navigate the AWS Management Console, and configure a budget alert to avoid unexpected charges.

## Architecture Diagram

This lab configures the foundational account structure you will use throughout the bootcamp. The components and their relationships are as follows:

```
Student's browser
    |
    v
AWS Management Console (us-east-1)
    |
    ├── Root account (secured with MFA)
    |
    ├── IAM service
    |       └── IAM user: bootcamp-admin (AdministratorAccess policy)
    |
    ├── AWS CloudShell (browser-based CLI)
    |
    └── AWS Budgets
            └── Zero spend budget (email alert)
```

The root account is the account owner identity. You will secure it with Multi-Factor Authentication (MFA) and then create a separate IAM user with administrative permissions for daily use. A zero spend budget provides an email notification if any usage exceeds the [AWS Free Tier](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html) limits.

## Prerequisites

- A valid email address that has not been used for another AWS account
- A phone number for identity verification
- A credit or debit card (required for account creation; you will not be charged for Free Tier usage)
- A virtual MFA application installed on your phone (for example, Google Authenticator, Microsoft Authenticator, or Authy)
- A modern web browser (Chrome, Firefox, Safari, or Edge)

## Duration

45 minutes

## Instructions

### Step 1: Create an AWS Account

If you already have an AWS account, skip to Step 2.

> **Warning:** Account creation requires a credit or debit card. AWS will not charge you for Free Tier usage, but charges apply if you exceed Free Tier limits or forget to delete resources after labs. The zero spend budget you create in Step 5 helps catch unexpected charges early.

1. Open your web browser and navigate to the [AWS sign-up page](https://signin.aws.amazon.com/signup?request_type=register).
2. Enter your email address in the **Root user email address** field.
3. Enter a name for your account in the **AWS account name** field (for example, `my-bootcamp-account`).
4. Choose **Verify email address**. AWS sends a verification code to your email.
5. Check your email inbox, copy the verification code, paste it into the verification field, and choose **Verify**.
6. Create a strong password for the root user and confirm it. The password must be at least 8 characters and include a mix of uppercase, lowercase, numbers, and symbols.
7. Choose **Continue**.
8. Select **Personal** (or **Business** if applicable) and fill in the required contact information.
9. Read and accept the [AWS Customer Agreement](https://aws.amazon.com/agreement/).
10. Choose **Continue**.
11. Enter your payment information (credit or debit card). AWS may place a temporary hold of $1 USD to verify the card.

> **Tip:** You will not be charged for services covered by the [AWS Free Tier](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html). The card is required for identity verification and for charges that exceed Free Tier limits.

12. Complete the phone verification step: enter your phone number, solve the CAPTCHA, and enter the PIN that AWS sends to your phone.
13. Select the **Basic Support (Free)** plan.
14. Choose **Complete sign up**.
15. Wait for the activation email from AWS. Activation typically takes a few minutes but can take up to 24 hours.

> **Troubleshooting:** If you do not receive the activation email within 15 minutes, check your spam/junk folder. If the email does not arrive after 1 hour, try signing in anyway at [console.aws.amazon.com](https://console.aws.amazon.com/). Some accounts activate before the email is sent. If you still cannot sign in after 24 hours, contact [AWS Support](https://aws.amazon.com/contact-us/).

16. Once activated, sign in to the [AWS Management Console](https://console.aws.amazon.com/) using your root user email and password.

**Expected result:** You see the AWS Management Console home page after signing in.

### Step 2: Secure the Root Account with MFA

The [root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html) has unrestricted access to your entire AWS account. Enabling [MFA on the root user](https://docs.aws.amazon.com/IAM/latest/UserGuide/enable-virt-mfa-for-root.html) is a critical security step.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as the root user.
2. In the top-right corner of the navigation bar, choose your account name, then choose **Security credentials**.
3. Scroll down to the **Multi-Factor Authentication (MFA)** section.
4. Choose **Assign MFA device**.
5. In the **Device name** field, enter a name (for example, `root-mfa-device`).
6. Select **Authenticator app** and choose **Next**.
7. AWS displays a QR code. Open your virtual MFA application on your phone and scan the QR code.

> **Tip:** If you cannot scan the QR code, choose **Show secret key** and manually enter the key into your authenticator app.

> **Troubleshooting:** If your authenticator app does not recognize the QR code, ensure your phone's camera has permission to access the browser. Alternatively, use the manual secret key option.

8. Your authenticator app begins generating six-digit codes that rotate every 30 seconds. Enter the current code in the **MFA code 1** field.
9. Wait for the code to rotate (up to 30 seconds), then enter the new code in the **MFA code 2** field. The two codes must be different and consecutive.

> **Troubleshooting:** If you receive an "invalid MFA code" error, check that your phone's clock is synchronized. MFA codes are time-based, and a clock skew of more than 30 seconds causes failures. On iPhone, go to Settings, General, Date and Time, and enable "Set Automatically." On Android, go to Settings, System, Date and Time, and enable "Automatic date and time."
10. Choose **Add MFA**.

**Expected result:** A success banner confirms that the MFA device has been assigned to the root user. The **Multi-Factor Authentication (MFA)** section now shows your registered device.

> **Warning:** Store a backup of your MFA configuration securely. If you lose access to your MFA device and have no backup, you will need to [contact AWS Support](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_lost-or-broken.html) to regain access to your account.

### Step 3: Create an IAM Administrative User

AWS [recommends](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) that you do not use the root user for everyday tasks. Instead, create an [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) with administrative permissions.

1. In the AWS Management Console, type `IAM` in the search bar at the top and select **IAM** from the results.
2. In the left navigation pane, choose **Users**.
3. Choose **Create user**.
4. In the **User name** field, enter `bootcamp-admin`.
5. Select the checkbox **Provide user access to the AWS Management Console**.
6. Select **I want to create an IAM user**.

> **Troubleshooting:** If you do not see the "I want to create an IAM user" option, AWS may default to IAM Identity Center. For this bootcamp, select the option to create an IAM user directly. If the console interface has changed, look for a link that says "Create IAM user" or similar.

7. Choose **Custom password** and enter a strong password. Optionally, clear the **User must create a new password at next sign-in** checkbox for convenience during the bootcamp.

> **Tip:** Write down or securely store the password you create. You will use this password to sign in as `bootcamp-admin` for every subsequent lab.
8. Choose **Next**.
9. On the **Set permissions** page, select **Attach policies directly**.
10. In the search box, type `AdministratorAccess`.
11. Select the checkbox next to the **AdministratorAccess** managed policy.
12. Choose **Next**.
13. Review the user details and choose **Create user**.

> **Verification:** After creation, choose the user name `bootcamp-admin` from the Users list. On the **Permissions** tab, confirm that the `AdministratorAccess` policy is listed. If it is missing, choose **Add permissions**, then **Attach policies directly**, search for `AdministratorAccess`, select it, and choose **Add permissions**.

14. On the confirmation page, note the **Console sign-in URL**. It follows this format:

```
https://<account-id>.signin.aws.amazon.com/console
```

> **Tip:** Your account ID is a 12-digit number (for example, `123456789012`). You can find it in the top-right corner of the console under your account name dropdown. Bookmark the sign-in URL. You will use it to sign in as your IAM user going forward.

15. Choose **Return to users list**.
16. Sign out of the root account: choose your account name in the top-right corner and select **Sign out**.
17. Navigate to the console sign-in URL you noted in step 14.
18. Sign in with the username `bootcamp-admin` and the password you created.

**Expected result:** You are signed in to the AWS Management Console as the `bootcamp-admin` IAM user. The account dropdown in the top-right corner displays `bootcamp-admin`.

### Step 4: Navigate the AWS Management Console

Now that you are signed in as your IAM user, take a tour of the [AWS Management Console](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/getting-started.html) interface.

1. **Services menu:** In the top-left corner, choose **Services**. Browse the categories: Compute, Storage, Database, Networking and Content Delivery, Security, Identity, and Compliance. Note how AWS organizes its 200+ services into logical groups.
2. **Search bar:** At the top of the console, use the search bar to find services quickly. Type `S3` and observe the search results. Press **Escape** to close the search without navigating.
3. **Region selector:** In the top-right area of the navigation bar, locate the [Region selector](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/select-region.html). It displays the currently selected Region. Choose it and select **US East (N. Virginia) us-east-1**. All labs in this bootcamp default to `us-east-1`.

> **Warning:** If you create resources in a different Region, you may not see them when viewing `us-east-1`. Always verify your Region before creating or searching for resources.

4. **CloudShell:** In the navigation bar, locate the [CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html) icon (a terminal/command-prompt icon, typically located near the Region selector and notification bell in the top-right area). Choose it to open a browser-based shell environment. Wait for the shell to initialize (this may take 15 to 30 seconds on first launch).

> **Troubleshooting:** If you do not see the CloudShell icon, ensure you are in a Region that supports CloudShell (us-east-1 supports it). If the icon is still not visible, type `CloudShell` in the console search bar and select it from the results. If CloudShell fails to launch, try refreshing the browser page or clearing your browser cache.
5. In CloudShell, run the following command to verify your identity:

```bash
aws sts get-caller-identity
```

Expected output:

```json
{
    "UserId": "AIDEXAMPLE123456",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/bootcamp-admin"
}
```

6. Confirm that the `Arn` field contains `user/bootcamp-admin`. This verifies that you are authenticated as your IAM user.
7. Run the following command to confirm your current Region:

```bash
aws configure get region
```

If the output is blank or does not show `us-east-1`, set it with:

```bash
export AWS_DEFAULT_REGION=us-east-1
```

Then verify:

```bash
echo $AWS_DEFAULT_REGION
```

Expected output:

```
us-east-1
```

> **Tip:** [CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/getting-started.html) comes with the AWS CLI pre-installed and pre-authenticated with your current console credentials. You do not need to configure access keys to use it.

### Step 5: Set Up a Zero Spend Budget

A [zero spend budget](https://docs.aws.amazon.com/cost-management/latest/userguide/budget-templates.html) sends you an email alert if any AWS usage exceeds the Free Tier limits. This is your safety net against unexpected charges.

1. In the console search bar, type `Budgets` and select **AWS Budgets** under the Billing and Cost Management section.

> **Tip:** If you see a message that you need to enable Cost Explorer, choose **Enable Cost Explorer**. It can take up to 24 hours for cost data to appear, but you can still create a budget immediately.

> **Troubleshooting:** If you receive an "access denied" error when navigating to Budgets, ensure you are signed in as `bootcamp-admin` (not the root user) and that the `AdministratorAccess` policy is attached. New accounts may also require a few hours before the Billing console is fully accessible to IAM users. If the issue persists, sign in as the root user to create the budget, then return to the IAM user for subsequent labs.

2. Choose **Create budget**.
3. Under **Budget setup**, select **Use a template (simplified)**.
4. Under **Templates**, select **Zero spend budget**.
5. In the **Budget name** field, enter `Bootcamp-Zero-Spend-Budget`.
6. In the **Email recipients** field, enter the email address where you want to receive alerts.
7. Choose **Create budget**.

**Expected result:** The Budgets overview page displays your new `Bootcamp-Zero-Spend-Budget`. The budget type shows as "Cost" and the budgeted amount shows as $0.01.

8. To verify, choose the budget name to view its details. You should see:
   - **Budget amount:** $0.01
   - **Alert threshold:** 0.01% of budgeted amount (actual)
   - **Notification:** Email to the address you specified

> **Warning:** The zero spend budget alerts you after charges are incurred, not before. Always check the [Free Tier usage tracking page](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/tracking-free-tier-usage.html) in the Billing console periodically to monitor your usage proactively.

## Validation

Confirm the following:

- [ ] You can sign in to the AWS Management Console using the root user email and password (plus MFA)
- [ ] The root account has an MFA device registered (visible under **Security credentials** for the root user)
- [ ] You can sign in to the AWS Management Console as the `bootcamp-admin` IAM user using the account sign-in URL
- [ ] Running `aws sts get-caller-identity` in CloudShell returns an ARN containing `user/bootcamp-admin`
- [ ] The Region selector in the console displays `us-east-1`
- [ ] A zero spend budget named `Bootcamp-Zero-Spend-Budget` exists in the AWS Budgets console

## Cleanup

This lab creates resources you will use throughout the bootcamp. Do not delete them until you have completed all modules. When you are finished with the bootcamp, follow these steps to clean up:

1. **Delete the zero spend budget:**
   - Navigate to the [AWS Budgets console](https://console.aws.amazon.com/cost-management/home#/budgets).
   - Select the checkbox next to `Bootcamp-Zero-Spend-Budget`.
   - Choose **Actions**, then **Delete**.
   - Confirm the deletion.

2. **Delete the IAM user:**
   - Navigate to the [IAM console](https://console.aws.amazon.com/iam/).
   - In the left navigation pane, choose **Users**.
   - Select the checkbox next to `bootcamp-admin`.
   - Choose **Delete**.
   - Type the username `bootcamp-admin` to confirm, then choose **Delete**.

3. **Remove root account MFA (optional):**
   - Sign in as the root user.
   - Navigate to **Security credentials** (choose your account name in the top-right, then **Security credentials**).
   - In the **Multi-Factor Authentication (MFA)** section, select your MFA device and choose **Remove**.
   - Confirm the removal.

> **Warning:** If you plan to close your AWS account entirely, first delete all resources across all Regions to avoid ongoing charges. Review the [closing your AWS account](https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-closing.html) documentation for the complete process.

## Challenge (Optional)

Using only the AWS Management Console and concepts from Module 01, complete the following:

1. Create a second IAM user named `bootcamp-readonly` with the `ReadOnlyAccess` managed policy (instead of `AdministratorAccess`).
2. Sign in as `bootcamp-readonly` and attempt to create an S3 bucket. Observe the "Access Denied" error.
3. Sign back in as `bootcamp-admin` and verify that you can access the S3 console without errors.

This exercise demonstrates the [principle of least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html): granting users only the permissions they need. You will explore IAM policies in depth in Module 02.

> **Tip:** Remember to delete the `bootcamp-readonly` user after completing the challenge to keep your account clean.
