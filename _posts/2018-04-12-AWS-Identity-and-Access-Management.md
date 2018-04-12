---
title: AWS&#58; Identity and Access Management
layout: post
---

Type "iam" into the AWS services search bar and click on the IAM service.

<figure>
  <img src="/images/aws-iam-1.png" width="400">
</figure>

To create a user, click on "Users" in the left-hand navigation bar, then on the next page
click on "Add User", and choose a username and access type:
* Programmatic Access will generate an access key and secret access key
* AWS Management Console Access enables a password sign-on for a user

Here, we give the user only "programmatic access," which means they cannot sign in 
through the AWS Console, but instead must use the AWS API, CLI, SDK, etc.  

<figure>
  <img src="/images/aws-iam-2.png" width="400">
</figure>

When you click on "Next: Permissions," you will come to a page where you can add the user to
a permissions group (among other options).  If you have not yet defined any user groups, you will
have the option to do so now: click on "Create Group", give the group a name, and assign the 
group a policy (either customized or preset).  Here, we give our new user full access to S3 (and
nothing else!).

<figure>
  <img src="/images/aws-iam-3.png" width="400">
</figure>

After group is created, click on "Next: Review" and then on the next page, "Create User".

Now that the user is  created, and has been added to the newly-created S3-related group (non-admin-s3), the user's
Access Key ID and Secret Access Key are available top copy down (or download a CSV).

<figure>
  <img src="/images/aws-iam-4.png" width="400">
</figure>

On the same screen, you will find a link that the user can use to sign in -- if that user was given the
username/password Console privilege, which our user was not.  Note that this sign-in URL is different than
the regular Console sign-in page that a root user signs into.  Also note that the identifier in the URL
is the account number, and it is generally a good practice to customize the URL and take out the 
account number (the less info you give out publicly, the better!).  To customize the URL, just
head back to the IAM main page where the IAM users sign-in link is displayed along with the option
to customize the link.

Back on the IAM main page, you can now see that you have at least one user and one group created.  

You can see the group policy, which is a JSON file.... Click around: you'll find it!

<figure>
  <img src="/images/aws-iam-5.png" width="400">
</figure>

You'll find that the user has permission only pertaining to S3 and no other AWS service.  To see that this
is true, create another user, but this time with Console sign-in permissions, and add them to the same group
(non-root-s3).  Then sign in as that user on the IAM user sign-in page and click around.

# Gist: Basically, this is like Linux Administration
This process is a GUI way of creating user and user groups, like I've done with Linux in the past.  However, it is more powerful
in that many default policies already exist (don't have to constantly reinvent the wheel or think through every
possibility, etc).

There is one email per AWS account, and that email/password combo represents the root user.  That is, 
if you are signing in with your email and password, you are acting as the almighty root or a demi-root 
with SUDO powers.  Just like signing into a Linux server as root is frowned upon, signing in at the AWS 
root is not considered a best practice.  To be safe, signing in as root should require multifactor
authentication (MFA), e.g., 2-step authentication.  The best practice is
to sign in as a user with much less power and permission, like the user account we just created.

Some might complain that they "need" to use the root account, but the Lynda instructor points out that 
[the lists of things](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)
that this is true for is actually quite small.

Best Practice:
* create user accounts
* create groups
* create policies
* associate policies with groups
* add users to groups (exercise law of least privilege!)
* set a password policy

Instead of logging in as root, it's better to create an admin account that has all but the rootiest of 
root permissions, and login as that. Also a good idea to have MFA activated for any admin account.

# Custom Policies
AWS default policies are good for learning and playing around with, but in a production environment, you
probably want to tailor the policies to specific needs and tradeoffs.

Thought this was funny (from Lynda video):
<figure>
  <img src="/images/aws-iam-6.png" width="300">
</figure>

# Further Reading / References
* Lynda Course: [AWS for DevOps: Security, Governance, and Validation](https://www.lynda.com/Amazon-Web-Services-tutorials/AWS-DevOps-Security-Governance-Validation/614310-2.html)
* [AWS Security Audit Guidelines](https://docs.aws.amazon.com/general/latest/gr/aws-security-audit-guide.html)
* [AWS Overview of Security Processes (White Paper)](https://aws.amazon.com/whitepapers/overview-of-security-processes/)
* [AWS Tasks that Require Root User](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)





