# iam - basic

[iam introduction](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26097996#lecture-article)
* global service, all accounts are root accounts upon creation. create a user to iam, try not to use your root...
* iam groups can be used to control permissioning
* json documents called policies 

```
# get policies by role
aws iam list-attached-role-policies --role-name test-lambda-role
{
    "AttachedPolicies": [
        {
            "PolicyName": "AWSLambdaBasicExecutionRole",
            "PolicyArn": "arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole"
        }
    ]
}

# show actual policy json
aws iam get-policy-version --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole --version-id v1
{
    "PolicyVersion": {
        "Document": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": [
                        "logs:CreateLogGroup",
                        "logs:CreateLogStream",
                        "logs:PutLogEvents"
                    ],
                    "Resource": "*"
                }
            ]
        },
        "VersionId": "v1",
        "IsDefaultVersion": true,
        "CreateDate": "2015-04-09T15:03:43+00:00"
    }
}

# there's sid (identifier), principal (arn) as well in statement keys
```

[iam hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098012#lecture-article)
* create a iam user and group. this is best practice even for solo dev, don't use root account, access stuff via an admin user, activate mfa
* can attach policies via group admin instead of directly
* reset user password (for some reason... i can't reset my own password even tho supposed to have policy to. skill issue?)
* generates a custom sign in url with the user account id

[iam policies](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26623458#lecture-article)
* iam policies structure

[iam policies hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098018#lecture-article)
* delete user from group... attach policy to user... hands on with `IAMReadOnlyAccess`
* create custom policies (better done thru terraformmmmm)

[iam mfa overview](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098020#lecture-article)
* define password policy
* require MFA: either virtual, physical like yubikey (U2F), hardware key fob, aws govcloud (provided by surepassid)

[iam mfa hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098028#lecture-article)
* clickoops setting up... i've done this on my root account etc

[aws access keys, cli and sdk](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098034#lecture-article)
* Management Console, protected by your username, password, and possibly multifactor authentication
* CLI, protected by access keys. this thing is pretty amazing
* SDK, appcode libraries e.g. stuff like boto3.... These are also protected by the same access keys. 
* gotta generate access keys through the console

[cli hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098056#lecture-article)
* create access key, configure with `aws configure`

```
aws iam list-users
{
    "Users": [
        {
            "Path": "/",
            "UserName": "github-actions-user",
            "UserId": "12342342432",
            "Arn": "arn:aws:iam::...",
            "CreateDate": "2025-06-13T07:46:59+00:00"
        },
...
```

[aws cloudshell region availability - optional hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098060#overview)
* terminal within aws that's free to use. it's similar to local terminal.
* persistent home directory
* can upload and download files thru the interface (save to local machine or txf to cloudshell env)

[iam roles for services](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098068#lecture-article)
* clickiops create role, attach policy, name the role. better to do this thru iac lol

[iam security tools](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098074#lecture-article)
* iam credentials report (account level) - click on the menu and download it. comes out as a csv. arn, username, is mfa active, etc
* iam "last access" (service level) - lists what services have been used, and when last used. fka access advisor

[iam summary](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/26098086#lecture-article)
1. IAM users should be mapped to actual physical users within your company. Each user will have a password for accessing the AWS Management Console.
2. Users can be organized into groups. Policies, which are JSON documents outlining permissions, can be attached to either users or groups to manage access rights.
3. Roles can also be created. These roles serve as identities, but are intended for AWS services such as EC2 instances rather than individual users.
4. For enhanced security, multi-factor authentication (MFA) can be enabled. Additionally, password policies can be set to enforce password strength and rotation for users.
5. AWS services can be managed using the Command Line Interface (CLI) or the Software Development Kit (SDK), which allows management through programming languages.
6. Access keys can be created to allow programmatic access to AWS services via the CLI or SDK.
7. IAM usage can be audited by generating an IAM credentials report and by using the IAM access advisor service to review permissions and usage.

# iam - advanced

[organisations](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078393#lecture-article)
* global service, manage multiple aws accounts. primary is **management account**, other accounts are **member accounts**. each account can only belong to ONE organisation
* consolidated billing across all accounts. main account holds payment method. reserved instances can also be shared across accounts
* got api to automate account creation within org. root organizational unit (OU), management account is inside. can create sub OUs, can nest OUs within each other (not like groups...)
* **service control policies** - IAM policies applied to specific OUs or accounts that restrict what users and roles can do within those accounts, everyone except management account which always has everything enabled. e.g. specific OU can have aws sagemaker disabled + full aws access. deny will always supersede. blocklist vs allowlist (specific services)

[orgs hands on](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078395#lecture-article)
* policy inheritance, create org and add accounts etc.

[iam advanced policies](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078397#lecture-article)
* any policy within IAM. `aws:SourceIp`, `aws:RequestedRegion` to restrict where api calls can be made, `ec2:ResourceTag` can control start/stop instances by tag key value pairs e.g. project: dataanalytics, `aws:PrincipalTag` is for iam user itself, `aws:PrincipalOrgID` likewise
* `MultiFactorAuthPresent`, bunch of s3 bucket permissions (bucket vs object level)

[iam resource based policies vs iam roles](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/33878964#lecture-article)
* do we attach the policy directly to the resource, or use iam role that has permissions to access the resource?
* if role, then original permissions are ignored; resource based policy allows principal to keep their original permissions
* e.g. eventbridge resource-based policy to allow invocation from the rule, as in lambda trigger. gotta specify it directly for eventbridge to access lambda. target must support resource based policy. if not (e.g. ec2 auto scaling), use the iam role. this is a bit messy still so always check. Lambda, SNS, SQS, and S3 buckets use resource-based policies.

[iam policy eval logic](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18630394#lecture-article)
* permission boundaries, which always supersede attached policy. must check. argh
* combine it with AWS org **service control policies** 
* logic: explicity deny is always denied. THEN org scp, then resouce based policy, then identity based policy, then iam permission boundaries, then session policies

```
We check all possible policies. If there is an explicit deny, then it is denied automatically. Then we look at the Organizations SCP: is there an allow? If yes, then go to the next step; if not, then it's a deny because it is an implicit deny. Then we look at resource-based policies, for example, applied to S3 buckets or SQS. Again, we check if there is an allow. Then we look at identity-based policies to see if there is an allow or implicit deny. Then we look at IAM permission boundaries, and finally session policies (which are more related to STS).

All these things are evaluated when making a specific IAM action. Only if all these things allow the action and do not explicitly deny it, then you will have a final decision of allow, and you will be able to perform your action. This overview helps you understand how security works in AWS.
```

[aws iam identity centre](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078409#lecture-article)
* any application that supports **SAML 2.0 integration**, single login access to ec2 windows instances
* identity providers: 
    * A built-in identity store within the IAM Identity Center.
    * A third-party identity provider such as Active Directory (AD), OneLogin, or Okta.
* only need to log in once to the IAM Identity Center portal to gain SSO access to multiple AWS accounts and business applications
* define **permission sets** in the Identity Center to specify which users have access to which resources. automatically creates corresponding iam roles for users
* **attribute based access control** - define IAM permission sets, then associate them with tags, rather than directly to users so access can be modified dynamically

[aws directory services](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/34466238#lecture-article)
* 3 main options: 
    * AWS Managed Microsoft AD: Allows you to create your own Active Directory in AWS. Users can be managed locally, and it supports multi-factor authentication (MFA). It also supports trust relationships with on-premise AD, enabling shared user authentication.
    * AD Connector: Acts as a proxy gateway to redirect authentication requests to your on-premise AD. It supports MFA, but users are solely managed on-premise.
    * Simple AD: An AD-compatible managed directory on AWS that does not use Microsoft Directory Services. It cannot be joined with an on-premise AD and is suitable for standalone AWS environments without on-premise AD.
* integration with IAM identity centre:
    * If your Active Directory is managed on AWS using Directory Services, integration is straightforward and out of the box. You simply configure IAM Identity Center to connect to your AWS Managed Microsoft AD.
    * If your Active Directory is self-managed on-premises, there are two options:
        * Establish a two-way trust relationship between your on-premise AD and AWS Managed Microsoft AD. This allows single sign-on integration via IAM Identity Center.
        * Use an AD Connector to proxy authentication requests from IAM Identity Center to your on-premise AD.
    * The choice depends on whether you want to manage users within AWS or only proxy authentication requests to your on-premise directory.
* for the exam: 
    * If you want to proxy users to an on-premise Active Directory, use AD Connector.
    * If you want to manage users in the cloud with multi-factor authentication, use AWS Managed Microsoft AD.
    * If you do not have an on-premise Active Directory and need a simple directory in AWS, use Simple AD.

[aws control tower](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/34465994#lecture-article)
* Control Tower functions as an additional layer on top of AWS Organizations, enhancing multi-account management capabilities.
    * Automates environment setup with just a few clicks.
    * Allows pre-configuration of all desired settings.
    * Enables ongoing policy management through guardrails.
    * Detects policy violations and can remediate them automatically.
    * Provides an interactive dashboard to monitor compliance across all accounts.
* uses SCPs again

