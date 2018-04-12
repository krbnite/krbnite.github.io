---
title: AWS&#58; Identity and Access Management
layout: post
---

Type "iam" into the AWS services search bar and click on the IAM service.

<figure>
  <img src="/images/aws-iam-1.png">
</figure>

To create a user, click on "Users" in the left-hand navigation bar, then on the next page
click on "Add User", and choose a username and access type:
* Programmatic Access will generate an access key and secret access key
* AWS Management Console Access enables a password sign-on for a user

<figure>
  <img src="/images/aws-iam-2.png">
</figure>

When you click on "Next: Permissions," you will come to a page where you can add the user to
a permissions group (among other options).  If you have not yet defined any user groups, you will
have the option to do so now: click on "Create Group", give the group a name, and assign the 
group a policy (either customized or preset).  Here, we give our new user full access to S3 (and
nothing else!).

<figure>
  <img src="/images/aws-iam-3.png">
</figure>

After group is created, click on "Next: Review" and then on the next page, "Create User".

A link for this user's sign will appear: for doing things with S3, this is how you should sign in.  The original
email and password you used to sign in have all the power -- you, sir, are the root user!  The best practice is
to sign in as a user with much less power and permission, like the user account we just created.

Now that the user is  created, and has been added to the newly-created S3-related group (non-admin-s3), the user's
Access Key ID and Secret Access Key are available top copy down (or download a CSV).

<figure>
  <img src="/images/aws-iam-4.png">
</figure>


Back on the IAM main page, you can now see that you have at least one user and one group created.  

You can see the group policy, which is a JSON file....


Gist: Basically, this is
a GUI way of creating user and user groups, like I've done with Linux in the past.  However, it is more powerful
in that many default policies already exist (don't have to constantly reinvent the wheel or think through every
possibility, etc).

There is one email per AWS account, and that email/password combo represents the root user.

