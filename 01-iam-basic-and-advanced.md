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

[organisations](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03/learn/lecture/18078393#learning-tools)