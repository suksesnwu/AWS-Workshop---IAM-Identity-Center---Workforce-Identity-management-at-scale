# AWS Workshop: IAM Identity Center - Workforce Identity management at scale
This workshop is designed to help you get familiar with IAM Identity Center (successor to AWS Single Sign-On) for managing your organization's workforce identity at scale across a multi-account AWS deployment.

## Creating Users and Groups in IAM Identity Center

This section guides you through creating users and groups in the native IAM Identity Center identity store. For external IDP configuration, refer to the extra credit section.

#### 1. Choose Your Identity Source:
- The identity source determines where IAM Identity Center looks for users and groups. By default, IAM Identity Center provides a built-in identity store for easy management.

#### 2. Manage Identities in IAM Identity Center:
- Users and groups created in IAM Identity Center's identity store are available only within IAM Identity Center.

### Steps to Create Groups and Users:

#### **Step 1: Create Groups**
- For the workshop, create two groups: **Administrators** and **readOnly**.
- Navigate to the **IAM Identity Center Console**.
- Choose **Groups** and click **Create Group**.
    - Provide a **Group Name** (e.g., Administrators).
    - Provide a **Description** (e.g., Group for administrator users).
    - Click **Create group**. A success banner will confirm the creation.
- Repeat the steps to create the **readOnly** group.

![groups](https://github.com/user-attachments/assets/af6aee44-1582-429c-a661-39dd424c311e)

#### **Step 2: Create Users**
- For the workshop, create two users: **adminUser** and **readOnlyUser**.
- Navigate to the **IAM Identity Center Console**.
- Choose **Users** under **Workplace pool** and click **Add User**.
    - Provide a **Username** (e.g., adminUser).
    - For the password, select **Generate a one-time password**.
    - Enter an **Email Address** (e.g., email+admin@domain.com) and confirm it.
    - Fill in **First name** and **Last name**.
    - Click **Next**.
    - Assign the user to the **Administrators** group.
    - Review the information and click **Add user**.
    - Save the **One-time password** provided.
- Repeat the steps to create the **readOnlyUser** with a unique email (e.g., email+readOnly@domain.com) and assign it to the **readOnly** group.

![users](https://github.com/user-attachments/assets/0dbd78e7-8841-4c84-88d3-69de69884f31)

### Notes:
- Users and groups created in IAM Identity Center can access multiple AWS accounts across AWS Organizations.
- You can also create users and groups using the **Identity Center Identity Store APIs**.

## Creating Permission Sets in IAM Identity Center

Permission sets allow you to define permissions centrally in IAM Identity Center and apply them across multiple AWS accounts. They are provisioned as IAM roles in each AWS account.

#### Steps to Create Permission Sets:

**Step 1: AdministratorAccess Permission Set**
1. Navigate to the **IAM Identity Center Console**.
2. Choose the **AWS Region** based on your event or personal preference.
3. Click on **Permission sets** under **Multi-account permissions**, then click **Create permission set**.
4. On the **Select permission set type** page:
   - Choose **Predefined permission set**.
   - Select the AWS-managed policy **AdministratorAccess**.
   - Click **Next**.
5. On the **Specify permission set details** page, leave the defaults.
6. Click **Next** and then **Create** on the review page to finalize.

**Step 2: readOnly Permission Set**
1. Repeat the steps for **Create permission set**.
2. On the **Select permission set type** page:
   - Choose **Predefined permission set**.
   - Select the AWS-managed policy **ViewOnlyAccess**.
   - Provide a **Permission set name** (e.g., readOnly).
   - Leave all other fields as default.
3. Click **Next**, review the details, and click **Create**.

You have now created both **AdministratorAccess** and **readOnly** permission sets. Next, proceed to the provisioning step.

![permission-sets](https://github.com/user-attachments/assets/0e3357c1-df86-4905-9ac4-822a151429d2)

### Provisioning Permission Sets in IAM Identity Center

After creating permission sets, they need to be provisioned (assigned) to AWS accounts and linked to users or groups. Follow the steps below to assign and provision permission sets for your users and groups.

#### Steps to Provision Permission Sets:

**Step 1: Provision AdministratorAccess**
1. Navigate to the **IAM Identity Center Console**.
2. Select **AWS accounts** and choose the account where you want to provision access.
3. Click **Assign users or groups**.
4. In the **Select users and groups** page:
   - Choose **Groups**.
   - Select **Administrators** and click **Next**.
5. In the **Select permission sets** page:
   - Select **AdministratorAccess**.
   - Click **Next**.
6. In the **Review and submit** page, review the information and click **Submit**.
7. A success banner will appear confirming the assignment of the **AdministratorAccess** permission set.

**Step 2: Provision readOnly**
1. In the **IAM Identity Center Console**, go to **AWS accounts** and select the same AWS account used earlier.
2. Click **Assign users or groups**.
3. In the **Select users and groups** page:
   - Choose **Groups**.
   - Select **readOnly** and click **Next**.
4. In the **Select permission sets** page:
   - Select **readOnly**.
   - Click **Next**.
5. In the **Review and submit** page, review the information and click **Submit**.
6. A success banner will confirm the successful provisioning of the **readOnly** permission set.

![aws accounts](https://github.com/user-attachments/assets/b68893f6-a8aa-4deb-835c-aa434ae62c02)

You have now successfully provisioned the **AdministratorAccess** and **readOnly** permission sets for the AWS account. The next step is to test the setup to ensure it works as expected.

### Testing Access Based on User, Group, and Permission Set Assignments

This section will guide you through validating the IAM Identity Center setup by deploying an AWS CloudFormation Stack. You will then test the access control by logging in with different users and performing actions based on their assigned permissions.

#### Step 1: Deploy the CloudFormation Template
1. **Download the Template:** Download the CloudFormation template provided for the workshop.
2. **Navigate to the CloudFormation Console:** 
   - Click **Create stack**.
   - Select **With new resources (standard)** to create the stack.
3. **Upload the Template:**
   - In the **Create stack** page:
     - Choose **Template is ready**.
     - Select **Upload a template file** for **Template source**.
     - Upload the downloaded file.
4. **Specify Stack Details:**
   - Name the stack (e.g., `iam-identitycenter-validation-setup`).
   - Leave the default parameters and click **Next**.
5. **Configure Stack Options:** Leave all options as default and click **Next**.
6. **Review and Create:** Scroll to the bottom and click **Submit**.
   - The deployment will take approximately 5 minutes.
   - Confirm that the stack status is **CREATE_COMPLETE**.

#### Step 2: Validate Access

**Testing with adminUser (AdministratorAccess Role):**
1. Open a private browser window or a different browser.
2. **Login into the AWS Access Portal** using the URL saved when creating the `adminUser`.
3. Enter the username and the one-time password for `adminUser`.
4. Set a new password for the user.
5. Once logged in, choose the **Management Console** link for the **AdministratorAccess** role.
6. Navigate to the **EC2 Console**.
7. Select a running instance and, under **Instance state**, choose **Stop instance**.
8. You should see a green banner confirming that the instance was successfully stopped.

![Stopping instance(admin)](https://github.com/user-attachments/assets/709bfca7-aab3-4bdb-95a4-f7f3a594ed26)

**Testing with readOnlyUser (readOnly Role):**
1. Log out from both the AWS Management Console and the AWS Access Portal.
2. **Login into the AWS Access Portal** with the `readOnlyUser`.
3. Enter the username and one-time password, then set a new password.
4. Choose the **Management Console** link for the **readOnly** role.
5. Navigate to the **EC2 Console** and attempt to stop a running instance.
6. You should see a red banner stating, "Failed to stop the instance."

![Stopping instance(readOnly)](https://github.com/user-attachments/assets/86a91599-78fd-4cae-b047-e1af7c2dbeb8)

This test demonstrates that the **AdministratorAccess** user can stop EC2 instances, while the **readOnlyUser** lacks permission to do so, validating the permission set assignments in IAM Identity Center.

### Clean Up

Once you have completed the workshop, it's important to remove the AWS resources created to avoid incurring any costs.

#### Clean Up CloudFormation Resources:
1. **Navigate to CloudFormation Service:**
   - Go to the CloudFormation console in your AWS account.
2. **Delete the CloudFormation Stack:**
   - Select the stack you created (with the name you provided, e.g., `iam-identitycenter-validation-setup`).
   - Click **Delete** to remove all associated resources.

For detailed instructions, you can refer to AWS CloudFormation documentation.

#### Clean Up Identity Center Resources:
1. **Navigate to IAM Identity Center Console:**
   - Ensure you are in the correct AWS Region (either the region suggested during an AWS event or the region where you configured your setup).
2. **Select Settings:**
   - From the left-hand menu, select **Settings**.
3. **Delete IAM Identity Center Configuration:**
   - In the **Management** tab of the **Settings** page, click on **Delete**.
   - Type the instance ID into the dialog box and click **Confirm** to delete the IAM Identity Center configuration.

This will clean up all the resources created during the workshop, ensuring no additional charges are incurred.
