---
title: The Skeletal Structure of HTML Reports
layout: post
---

Let's say you want to automate some HTML reports to be emailed daily. The reports should
have a consistent look, but clearly need to be dynamically generated to take into account
newly collected data and observations. This little tidbit should get you started!

Start with an email template, or what I call the report's skeletal structure. This can
be big, long string written out in your Python script, but I find that it's neater and
more organized to put this in a separate text file. What's important is making sure
you can "flesh out" this skeleton with variables at runtime.  Below, I use curly brackets
with variable names, like so:

```python
'My name is {name}. What a {adjective} day!'.\
  format(name='Kevin', adjective='wonderful')
```

Here is a bare-bones, basic skeleton. No frills.  Doesn't even follow best practices,
e.g., including relevant metadata.  That stuff can all come later when you get the idea
down and you're tweaking your code.

```html
<html>
<head></head>
<body>
  
  <h1>{name} on Social Media Site: {data_date}</h1>
  <br/><hr/>
  
  <h2>Metric One</h2>
  <table>
    <tr><td> Aggregate: {metric1_agg} </td></tr>
    <tr><td> Comparison: {metric1_kpi} </td></tr>
  </table>
  
  {data_table1}
  <br/><hr/>
  
  <h2>Metric Two</h2>
  <table>
    <tr><td> Aggregate: {metric2_agg} </td></tr>
    <tr><td> Comparison: {metric2_kpi} </td></tr>
  </table>
  
  {data_table2}  
  <br/><hr/>
  
  <table>
    <tr><td><b> Kevin Urban </b></td></tr>
    <tr><td> Data Scientist | CorporatePigSwine HQ </td></tr>
  </table>
</body>
</html>
```

Notice the curly brackets, like {name}, {data_date}, {data_table1}, 
and so on. These are placeholders.  They are reserved locations for some variables that will 
be computed during the execution of the main Python script. From Python, the variables can be inserted 
like so:

```python
with open('email_template.txt', 'r') as f:
  prehtml = f.read()
html = prehtml.format(
  name = product_name,
  data_date = data_date,
  metric1_agg = metric1_agg,
  metric1_kpi = metric1_kpi,
  data_table1 = data_table1,
  metric1_agg = metric1_agg,
  metric1_kpi = metric1_kpi,
  data_table1 = data_table1
)
```

