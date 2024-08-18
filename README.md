# AWS WAF Configuration

## Lab Mission
Implement security configuration using WAF to prevent unauthorized access to content saved in the cloud.


## Resources
- **Environment & Tools**
  - Web Browser
  - AWS Windows Server 2016 EC2 instance
- **Extra Lab Files**
  - `index.html`
- **Extra Links**
  - [AWS Documentation](https://docs.aws.amazon.com/)

## Lab Task 1: Configure an S3 Bucket
AWS S3 (Simple Storage Service) stores data in buckets that are accessible from anywhere. In this task, you will create a storage server with a bucket that contains a file (provided with the lab) that can be accessed by anyone over the internet.

> **Important:** Remember to undo all changes to the AWS account to avoid monetary fees!

1. Go to [AWS](https://aws.amazon.com) and log in to your account.
2. Navigate to **S3** under Storage services.
3. In the S3 buckets page, click **Create bucket**.
4. Give the bucket a name and click **Next**.

   > **Notes:** Since buckets can be shared globally, their names must be unique and all lowercase. Don’t select values in the other fields.

5. Leave the **Object Ownership** as default.
6. The **Block Public Access** setting will be opened. Select the checkbox acknowledging that it will become public.
7. Leave the **bucket versioning** as disabled and keep the default settings.
8. At the bottom, click **Create bucket**.
9. Click the name of the newly created bucket to access its page.
10. Click **Upload** to start uploading files to the bucket.
11. Click **Add files** and select the provided file `index.html` to upload. At the end of the process, click **Upload**.

    > **Note:** You can also drag and drop the file.

12. Verify that the file was uploaded.

## Lab Task 2: CloudFront Configuration
CloudFront is a content delivery service that you will use to deliver the contents saved in the bucket. The process involves creating a new distribution domain.

1. Return to the AWS management console and navigate to the **CloudFront** service under **Networking & Content Delivery**.
2. Click **Create distribution** to start working with AWS CloudFront.
3. Enter the distribution’s origin domain name. Then, at the bottom of the page, fill in the **default root object** with the name of the file uploaded to the `index.html` bucket and click **Create distribution**.

   > **Note:** The origin domain name must match the name of the bucket created in the previous task.

4. Verify that the distribution was created and remember the domain name for later use.

   > **Note:** It will take some time for the distribution to change its status to *Deployed*.

## Lab Task 3: WAF Configuration
The WAF enables control over which traffic from web applications is permitted and blocked (in our case, CloudFront content), according to predefined rules. In this task, only our EC2 will have access. All others will be blocked.

1. Make sure your EC2 instance is running.
2. Return to the AWS management console and navigate to the **WAF & Shield** service under **Security, Identity, & Compliance**.
3. Switch the view to **AWS WAF Classic**.
4. Navigate to **IP addresses** in the menu on the left to create an IP address to match a condition. Then, click **Create condition**.
5. Name the condition `Allowed-IP`, set the region to `Global (CloudFront)`, and set the IP address to the public IP of the EC2 server. Click **Add IP address or range** and then click **Create**.

   > **Note:** The IP address of the EC2 server appears in the EC2 dashboard.

6. Verify that the IP match condition was created. Navigate to **Web ACLs** in the menu on the left to create a firewall rule.
7. Click **Create web ACL**.
8. Name the ACL `AllowMe` and set the CloudFront distribution as the resource for association. Click **Next** to continue.

   > **Note:** The CloudFront resource name should appear when you click in the field.

9. In the **Create conditions** window, note the previously created `Allow-IP` condition is listed. Scroll down to the bottom of the page and click **Next**.
10. In the **Create rules** window, click **Create rule**. Name the new rule `AllowMeRule` and assign the **Regular** rule type. Under **Add conditions**, select `does`, `originate from an IP address in`, and `Allow-IP`. Then, click **Create**.
11. In the **Create rules** window, set the rule’s action to **Allow** and select **Block all requests that don’t match any rules**. Then, click **Review and create**.
12. Verify the ACL configuration and click **Confirm and create**.

## Lab Task 4: Verify WAF
This task is the proof of concept for WAF operation. The EC2 instance should be able to see the contents of CloudFront, while all other clients should not be able to view it.

1. Connect to your EC2 instance via RDP (as you did in CLD-01-L1 AWS Instance Setup, Task 4).
2. Open Internet Explorer by clicking its icon at the bottom. When prompted for security settings, select **Don’t use recommended settings** and click **OK**. Make sure to copy the link from CloudFront found in **Networking & Content Delivery**.
3. Browse to the domain address of CloudFront, select **Continue** to prompt when website content is blocked, click **Add**, and then click **Add** again in the window that appears.
4. On your personal computer, browse to the same address. You will notice that access is blocked due to the operation of the configured WAF.
5. Disconnect from the RDP connection.
6. Make sure to stop the EC2 instance. Also, terminate (delete) the instance, since this is the last lab of the course.
7. To delete the S3 bucket, navigate to the S3 service, select the bucket, and click **Delete**.
8. To delete the CloudFront distribution, navigate to the service, disable the distribution, and delete it.

   > **Note:** The process of disabling the distribution may take up to 15 minutes.

9. Navigate to the **WAF & Shield** service and click the `AllowMe` ACL. Click the **Rules** tab and then click **Edit web ACL**. Remove the rule and update the ACL.
10. Click **Rules** in the menu on the left. Open and click `AllowMeRule`, click **Edit rule**, remove the IP address, and update it.
11. Select `AllowMeRule` and click **Delete**.
12. Navigate to **IP addresses** in the menu on the left. Click `Allowed-IP` and then click **Delete**. Note the IP address and delete it. Make sure `Allowed-IP` is selected and click **Delete**.
13. Navigate back to **Web ACLs** and delete the `AllowMe` ACL.
