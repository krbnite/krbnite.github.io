---
title: Creating and Modifying CookieCutter Project Templates
layout: post
tags: data-science easi
---

A while back, I wrote about [CookieCutter Data Science](https://krbnite.github.io/Cookiecutter-Data-Science/), which
a project templating scheme for homogenizing data science projects.  The data science cookiecutter was a great idea, I think, 
and my team uses it for all our projects at work.  The 
key is in encouraging/enforcing a certain level of standards and structure.  It takes a little cognitive load to 
take on the data science cookiecutter your first time, but ever thereafter it will lighten the cognitive load for
you and your team.  This is essential when working on multiple projects at once, leaving projects idle for weeks to
months at a time, and frequently swapping projects with others on the team.  


CookieCutter, in general, can be used as
a homogenization tool for any type of project templating.  For example, in addition to the [data science cookiecutter](https://github.com/drivendata/cookiecutter-data-science),
a data scientist might be interested in 
[project templating for Python packages](https://github.com/audreyr/cookiecutter-pypackage).  But note that the 
templating needn't be for anything related to Python, data science, or programming.  It could be that every time
you have a new client, you begin their records with the same Microsoft Word and csv files.  Or, you might
write the same boilerplate for blog posts written in Markdown over and over again.  Both can be cookiecutter'd.  

In what follows, I will show how to create project templates for these "toy" examples.  Neither are incredibly
strong cases for learning how to use Cookiecutter, but include enough of the basics for when one comes up.  In my 
experience, this has become important for modifying and extending the original data science cookiecutter:  as I've
used this template for a while now, I have found myself having to make the same modifications and additions 
each time (e.g., in the Makefile, we need to include security flags on the AWS data transfers; e.g., we use additional
Markdown files for documentation; etc).


# New Client

First, make a directory whose name will be the name of your cookiecutter.  Then, inside this
directory, we will create the template directory and a JSON file that houses some creation-time
variables.  Inside the template directory, we will house a few standard documents that we use
every time we call a new potential client.

{% raw %}
```
mkdir newClient
cd newClient
mkdir {{cookiecutter.name}}
```
{% endraw %}

Inside the directory, {% raw %}`{{cookiecutter.name}}`{% endraw %}, we will put a MSWord file that includes a list 
of talking points and questions to ask.  We will also put a CSV file that will collect the relevant
data to be injected into our client database.  The MSWord file will have a variable name and the CSV
file will include a bunch of variables, all of which will be instantiated at the time of creation.

Ultimately, the CookieCutter template should look something like this:

{% raw %}
```
tree newClient/
newClient/
├── cookiecutter.json
└── {{cookiecutter.name}}
    ├── client_record.csv
    └── {{cookiecutter.name}}_First-Phone-Call_{%\ now\ 'local',\ '%Y-%m-%d'%}.docx

1 directory, 3 files
```
{% endraw %}

The CSV file will look something like this:
{% raw %}
```
cat newClient/\{\{cookiecutter.name\}\}/client_record.csv 
name,dob,gender,number,email
{{cookiecutter.name}},{{cookiecutter.dob}},{{cookiecutter.gender}},{{cookiecutter.number}},{{cookiecutter.email}}
```
{% endraw %}

And the top-level JSON file should look like this:
```json
cat cookiecutter.json 
{
  "name": "",
  "dob": "",
  "gender": "",
  "number": "",
  "email": ""
}

```

Now, to be able to call this CookieCutter from any path in your system, just move it into 
the CookieCutter hidden directory in your home folder:
```
mv newClient/ ~/.cookiecutters/
```

Adding a new client directory is now as easy as this:
```bash
cookiecutter newClient
name []: Joe Schmoe
dob []: 2019-02-02
gender []: f
number []: 1234567890
email []: e@m.ail
```

Now, believe me: this cookiecutter is doing the bare minimum. For example, it does not enforce
any format on anything: we could write the dob in any date format we want, etc.  These things can
be enforced using pre- and post-generation hooks.  We will use some very simple hooks in the
next example to illustrate.


# New Blog Post
Whenever I create a new blog post, I must create the same boilerplate Markdown -- basically this:
```
---
title: Catchy Title for Interesting Topic
layout: post
tags: cat dog fish
---
```

Also, how my GitHub blog is set up, the file name should have the date (YYYY-MM-DD) preceding the 
title... If I forget to do any of these things, the blog post might not look right, or might not
show up in the right spot, etc.  So, there you go: a little bit of motivation for creating a blog
post template!

What's new here is that I just want to create a file template.  However,
one thing I found when trying to create this example is that, by default, CookieCutter demands
that your CookieCutter creates a project directory.  With some finagling and Googling, I found that
you can use some simple post-generation hooks to get around this.

I'll show you the directory tree, cookiecutter.json, and hooks necessary to accomplish this.

First, the tree:
{% raw %}
```
tree newBlogPost/
newBlogPost/
├── cookiecutter.json
├── hooks
│   └── post_gen_project.sh
└── {{cookiecutter.formatted_title}}
    └── {{cookiecutter.dated_formatted_title}}.md

2 directories, 3 files
```
{% endraw %}

The JSON file:
{% raw %}
```json
{
  "title": "TITLE",
  "formatted_title": "{{ cookiecutter.title|replace(' ','-') }}",
  "dated_formatted_title": "{% now 'local', '%Y-%m-%d' %}-{{cookiecutter.formatted_title}}",
  "layout": "post",
  "tags": ""
}
```
{% endraw %}

The blog template:
{% raw %}
```
cat newBlogPost/\{\{cookiecutter.formatted_title\}\}/\{\{cookiecutter.dated_formatted_title\}\}.md 
---
title: {{cookiecutter.title}}
layout: {{cookiecutter.layout}}
tags: {{cookiecutter.tags}}
---

```
{% endraw %}

And, finally, the post-generation hooks that remove the directory and leave you with only
a new file:
```
cat newBlogPost/hooks/post_gen_project.sh 
#!/bin/bash
cp -rf . ..
rm -rf -- "$(pwd -P)"
```

# New Data Science Template
The point of learning all of this (for me!) was to figure out how to modify the original cookiecutter-data-science
project template to better suit the needs of me and my team.  The approach is simple:

1. In `~/.cookiecutters/`, duplicate `cookiecutter-data-science` and give it new name
2. Add in, subtract, and modify whatever necessary
3. Upload it to team's GitLab w/ a few directions on how to install it
4. Modify it over time if necessary; or branch it, if that's a better option

# References
* https://cookiecutter.readthedocs.io/en/latest/advanced/hooks.html
* https://cookiecutter.readthedocs.io/en/latest/advanced/templates_in_context.html
* https://cookiecutter.readthedocs.io/en/latest/advanced/template_extensions.html
* https://github.com/audreyr/cookiecutter/issues/35

# Addendum: Curly Braces on a Jekyll Blog
So, apparently all the CookieCutter curly braces lying around upset the Jekyll build operation.  [Thanks to
the internet](https://github.com/jekyll/jekyll/issues/6217), I quickly found out how to resolve this.

Solution:  have to add some additional Jekyll tags to indicate the braces have to be treated as is (raw).
