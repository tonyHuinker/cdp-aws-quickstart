# cdp-aws-quickstart

![CDP Landing Page](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/landingpage.png?raw=true)

If you've reached the above landing page for the first time, you've come to the right place! In this quickstart, we're going to walkthrough step by step how to connect CDP to your AWS account so that you can begin to provision clusters and workloads. 

In order to complete this quickstart, you'll need access to two things.  

  1. The CDP console (if you've reached the above screen, you're good to go there)
  2. The AWS console

   

#### Step 1. Creating a CDP Credential

  - In the CDP Console, the first thing we're going to do is create our CDP Credential.  The CDP credential is the mechanism that allows CDP to create resources inside your Cloud Account.  
    1. From the CDP Home Screen, click the **Management Console** icon. 
    2. On the left side navigation plane, go to **Environments**
    3. From there, in the top left choose **Shared Resources**, then **Credentials**
    4. Click on the **Create Credential** button on the top right.

![CDP Credential Page](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/credential.png?raw=true)
    

- In the AWS Console (keep the CDP screen open a different tab or window), we're going to create the policy that will be applied to the CDP role. 
   
   1. In AWS, navigate to **IAM**
   2. Click on **Policies**
   3. Click on **Create policy**
   4. Choose **JSON**, and paste the provided json from the *Create Cross-account Access Policy* from the **CDP Management Console**
   5. Click **Review Policy**
   6. Give it a *name and description*. The name can be any valid name.
   7. Click **Create policy**. 
- Staying in the AWS Console, we'll now create the required role. 
   1. In AWS, navigate back to **IAM**
   2. Click on **Roles**
   3. Click on **Create role**
   4. Under *Select type of trusted entity*, choose **Another AWS Account**
   5. Copy and paste the *Service Manager Account ID* from the *CDP Management Console*, and paste it **Account ID*** field. 
   6. Check **Required external ID**
   7. Copy and paste the *External ID* from the *CDP Management Console*, and paste it into the **External ID** field. 
   8. Now, for the name of the policy you created above, and apply it by **checking the checkbox next to it**. 
   9. Choose **Next: Tags**
   10.  Choose **Next: Review**
   11. Give the role a *name* and *description*.  The name can be any valid name. 
   12. Click **create role**
   13. Staying in the role page in the *AWS Console*, search for the role you just created, and **click on it**
   14. At the top of the screen, copy the **Role ARN**

![Copy Role ARN](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/copyrolearn.png?raw=true)

   

- Head back to the CDP Management console for the final steps in creating the credential. 
  1. Give your CDP credential a name and description.  The name can be any valid name. 
  2. Paste the *role ARN* you copied from the AWS management console, and paste it into the **Cross-account Role ARN**

![Paste Role ARN](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/pasterolearn.png?raw=true)


#### Step 2. Creating a CDP Environment 

  - We'll want to create specific IAM roles and policies for CDP to operate in a secure manner.  For background info, a description of what we're building and why can found [here](https://docs.cloudera.com/management-console/cloud/environments/topics/mc-idbroker-minimum-setup.html).  For this quickstart, we'll be using CloudFormation to set all of this up for you.
      - Download the provided CloudFormation template [here](https://raw.githubusercontent.com/tonyHuinker/cdp-aws-quickstart/master/cloudformation/setup.json) 



- In the *AWS Console*, we're now going to deploy our CloudFormation template.  
     1. In *AWS Services*, search for **CloudFormation**
     2. Click **Create Stack** in the top right
     3. Choose **template is ready**, and **upload a template file** ![Create Stack](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/createstack.png?raw=true). 

     4. Select the template file you just downloaded.
     5. Click **Next**
     6. Enter your stack name.  This can be any valid name.  Below you should change
        - S3BucketName - choose an unused bucket name, CDP will be creating the bucket for you
        - AWSAccount - your 12 digit AWS account ID (can be found [here](https://console.aws.amazon.com/billing/home?#/account)). 
        - prefix - a short prefix of your choosing to add to the names of the IAM resources we'll be creating. 
         
        ![parsed](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/stackparsed.png?raw=true).  

	 7. Click **Next**. 
     8. At the *Configure Stack Options* page, click **Next**
     9. At the bottom of *Review page*, under capabilities, we need to click the checkbox next to **I acknowledge that AWS CloudFormation might create IAM resources with custom names**, as that is exactly what we will be doing.
       ![Acknowledge](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/ack.png?raw=true).  

     10. Click **Create stack**



- One last thing, in the *AWS Console*, we'll want to create an SSH Key in the region of your choice.  If there is already an SSH key in your preferred region you'd like to use, you can skip these steps.  
   1. In *AWS Services*, search for **EC2**
   2. Doublecheck that you are in your preferred region in the top-right corner. 
   3. On the left-side navigation bar, choose **Key Pairs**
   4. On the top right, choose **Create Key Pair**
   5. Provide *Name* and choose **pem** format.  The name can be any valid name.

- Back in the *CDP Management Console*
    1. Navigate to **Environments**
    2. Click **Register Environment**
    3. Provide an environment name and description.  The name can be any valid name. 
    4. Choose *amazon* as the Cloud Provider
    5. Under *Amazon Web Services Credential*, chose the credential we created earlier. 
    6. Click **Next**
    7. Under *Data Lake Settings*, give your new Data Lake a name.  The name can be any valid name. Choose the latest Data Lake Version
    8. Choose *Light Duty* for Data Lake scale. 
    9. Click **Next**
    10. Choose your desired **region**, this should be the same region you created an SSH Key in above. 
    11. Under *select network* choose **Create New Network**
    12. Under *Security Access Settings* choose **Create New Security Groups**
        ![Region](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/regionnetwork.png?raw=true). 
        
    13. Under *SSH Settings*, choose the SSH key created earlier. 
    14. Under *Logs - Storage and Audit*, choose the Instance Profile we mentioned to save earlier, titled **prefix-log-access-instance-profile**, for logs location base choose **S3BucketName/logs**, and for *Ranger Audit Role* choose **prefix-ranger-audit-role**
        ![logs](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/logs.png?raw=true).
    15.  Under *Data Access*, choose the **prefix-data-access-instance-profile**, for *Storage Location Base* choose **S3Bucketname**. 
        ![data](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/data.png?raw=true).
    16. (optional) Provide any tags you'd like these resources to be tagged with. 
    17. Under *Enable S3 Guard*, enter **prefix-dynamodb-table**
       ![dynamo](https://github.com/tonyHuinker/cdp-aws-quickstart/blob/master/screenshots/dynamo.png?raw=true).
    18. Click **Register Environment**
