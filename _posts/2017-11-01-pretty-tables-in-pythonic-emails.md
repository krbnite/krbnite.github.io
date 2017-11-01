---
title: Pretty Tables in Pythonic Emails

So you have a pandas DataFrame.  Great!

Now make it look pretty in a PDF file, or an email.  How do you do it?


## First Shot: Tabulate
The first way I figured out was to use the tabulate module.

```
pip install tabulate
```

```python
html_table = tabulate(df, headers=tbl.columns, tablefmt="html") 
text_tables= tabulate(tbl, headers=tbl.columns, tablefmt="grid") 
```

## Second is the Best: Pandas
Then I found out you never really need to leave pandas!

I mean, yea, I knew about df.to_csv() and df.to_pickle(), but I guess I was ignorant to all the other 
amazing to_xyz() functions.

Here are just a few:
```python
df.to_string()
df.to_html()
df.to_csv()
df.to_excel()
df.to_latex()
df.to_json()
df.to_dict()
df.to_clipboard()

# Remove that index
df.to_html(index=False)
```

There are actually quite a few more than this, but that should be enough to make my point!

## Pandas & Cascading Style Sheets
Even better, you can format your pandas DataFrames.  It's pretty crazy.

For example, 
```python
df.style.render()
```

This is different than `df.to_html()` in that the latter produces bare-bones HTML for your table, while
the former is anything but bare bones...

More importantly, `df.style.render()` generalizes quite a bit and gives a lot of control.  To be fair, it seems
that the `to_html()` method also has a bit more power if you want it.


### Get rid of index 
```python
styles = [ dict(selector = "th:first-child", props = [('display', 'none')]) ]
df.style.set_table_styles(styles).render()
```

### Get cool bar plot background
```python
df.style.bar().render()
```

### WTF
Everything looked beautiful on my Mac's Outlook... Then I went to show off to a co-worker who has a PC.

Short story short: The email did not look beautiful on his PC.  

I then began testing on the web version of Office 365 / Outlook... It, too, did not receive the email
in a beautiful state.  Also, add Gmail to the list...and likely any email platform I was not actively
testing on all day.

Where did I go wrong?  Well, it's just like when I used to play with website design: if your only test you website on Chrome,
it will probably still look OK in Safari, though you should be thinking cross-platform from the get-go).  But without
a stroke of magic, luck, or both, your site might not even work on Internet Explorer.  Turns out, you need to think
cross-platform from the get-go when designing HTML emails as well...which makes sense, but is annoying AF.





--------------------------------------------------------------------------

**Pandas References**:
* [pandas Styler: How to ignore the index column from the rendered HTML](https://stackoverflow.com/questions/34714145/pandas-styler-how-to-ignore-the-index-column-from-the-rendered-html)
* [How to use `style` in conjunction with the `to_html` classes on a DataFrame?](https://stackoverflow.com/questions/42629171/how-to-use-style-in-conjunction-with-the-to-html-classes-on-a-dataframe)

**References on HTML Emails to Look Into**:
* [The Do's and Donts of Cross-Platform Email Design](https://sendgrid.com/docs/Classroom/Build/Format_Content/html_rendering__the_dos_and_donts_of_cross_platform_email_design.html)
* [Building HTML Email and Workflow Tips](http://blog.mailgun.com/building-html-email-and-workflow-tips/)
* [How to Create a Dynamic HTML Email Template](http://www.assafelovic.com/blog/2016/4/29/how-to-create-a-dynamic-html-email-template)




