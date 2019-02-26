---
title: Google Developer Scholarship&#58; Back to the Basics of HTML and CSS
layout: post
tags: webdev grow-with-google udacity wwe
---

I hack together HTML reports and HTML emails, but some parts of the web development class and
the intro-level JavaScript courses made me ask, "But do I really know WTF I'm doing?"  

Nope.

I'm glad that I was able to be honest with myself about this b/c I actually learned so much
about parts of HTML and CSS that I either forgot, or simply never knew about.  

My [course notes](https://github.com/krbnite/GoogleDeveloperScholarship2018/tree/master/Round1/Supplementary-Courses/02__HTML-and-CSS)
on Udacity's [HTML and CSS Syntax](https://eu.udacity.com/course/html-and-css-syntax--ud001) course.

-----------------------------------------------

# Lessons Learned
## Reminder About ID and Class Syntax
Using just a selector, like p, any style is applied to all matching tags in the corresponding
HTML document.  But what if we have different sections of the webpage that should have different stylings?

Solution: We can use the tag attributes "id" and "class".

### ID
IDs are special and should be used sparingly: 
* an HTML element can have only one ID
* a specific ID can only be used once per page
```css
#site-description {
  color: red;
}
```

### Class
More generally, you will style collections of elements -- and for this, you need the class attribute.
```css
.book-summary {
  color: blue;
}
```

## So Much I Actually Didn't Know About CSS Selectors
In the previous section, we covered 3 types of CSS selector: tag, id, and class.  Apparently,
there are more!  Learn about them on the [MDN Selectors Page](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Selectors).

### Multiple Selectors, One Declaration Block
Here's something I learned: if you have a few selects, say tags, that you want to style the same,
you can throw them in the same selector group:
```css
div p, #id:first-line {
  background-color: red;
  background-style: none;
}
```

### So many types of selectors!
Straigth from MDN's mouth:
> Selectors can be divided into the following categories:
>
> * **Simple selectors**: Match one or more elements based on element type, class, or id.
> * **Attribute selectors**: Match one or more elements based on their attributes/attribute values.
> * **Pseudo-classes**: Match one or more elements that exist in a certain state, such as an element that is being hovered over by the mouse
>   pointer, or a checkbox that is currently disabled or checked, or an element that is the first child of its parent in the DOM tree.
> * **Pseudo-elements**: Match one or more parts of content that are in a certain position in relation to an element, for example the first 
>   word of each paragraph, or generated content appearing just before an element.
> * **Combinators**: These are not exactly selectors themselves, but ways of combining two or more selectors in useful ways for very specific 
>   selections. So for example, you could select only paragraphs that are direct descendants of divs, or paragraphs that come directly 
>   after headings.
> * **Multiple selectors**: Again, these are not separate selectors; the idea is that you can put multiple selectors on the same CSS rule, 
>   separated by commas, to apply a single set of declarations to all the elements selected by those selectors.

This stuff is pretty cool!  I really didn't know how many types of selectors there are.

### Attribute Selectors 
Basically, I'm tempted copy-and-paste this entire page, but just look at it: [https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Attribute_selectors](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Attribute_selectors)

I wonder if these are ways I could select elements with Selenium; webscraping could be so much easier and more
robust against minor updates in a website's code (e.g., adding a value to a class has crashed my scripts in the past).

### Simple Attribute Selectors
* [attr]: will select any element with attribute attr, whatever its value
* [attr=val]: will select any element with attribute attr, but only if its value is val
* [attr~=val]: will select any element with attribute attr, but only if the value val is one of a space-separated list contained in attr's value

### Regex-Like Attribute Selectors
* [attr|=val]: select all elements with the attribute attr for which the value is exactly val or starts with val- (careful, the dash here isn't a mistake, this is to handle language codes)
* [attr^=val]: select all elements with the attribute attr for which the value starts with val
* [attr$=val]: select all elements with the attribute attr for which the value starts with val
* [attr\*=val]: select all elements with the attribute attr for which the value contains the string val (unlike [attr~=val], this selector doesn't treat spaces as value separators but as part of the attribute value)

### Pseudo-Class and Pseudo-Element Selectors
> A **CSS pseudo-class** is a keyword, preceded by a colon (:), added to the end of selectors to specify you want to style the selected elements, and only when they are in certain state. For example, you might want to style an element only when it is being hovered over by the mouse pointer.

Examples
* :active
* :hover
* :target
* :first-child

> **Pseudo-elements** are very much like pseudo-classes, but they have differences. They are keywords, this time preceded by two colons (::), that can be added to the end of selectors to select a certain part of an element.  They are specific to, for example, a tag's content.

Examples
* ::after
* ::before
* ::first-letter
* ::first-line
* ::selection
* ::backdrop

## An Example of How Much I Actually Don't Know About HTML
Buttons are simple enough:
```html
<button>This is a button</button>
```

I learned something new when looking them up on the MDN page about 
[buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button), namely that
they accept "phrasing content."
 
Well, shiz, what is phrasing content?
 
### Phrasing Content
Elements belonging to the [phrasing content](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_categories#Phrasing_content)
category are: \<abbr>, \<audio>, \<b>, \<bdo>, \<br>, \<button>, \<canvas>, \<cite>, \<code>, \<command>, \<data>,
\<datalist>, \<dfn>, \<em>, \<embed>, \<i>, \<iframe>, \<img>, \<input>, \<kbd>, \<keygen>, \<label>, \<mark>, \<math>,
\<meter>, \<noscript>, \<object>, \<output>, \<progress>, \<q>, \<ruby>, \<samp>, \<script>, \<select>, \<small>, 
\<span>, \<strong>, \<sub>, \<sup>, \<svg>, \<textarea>, \<time>, \<var>, \<video>, \<wbr>, and plain text.
 
Other elements belong to this category, but only if a specific condition is filled:
* \<a> if it contains only phrasing content
* \<area> if it is a descendant of a \<map> element
* \<del> if it contains only phrasing content
* \<ins> if it contains only phrasing content
* \<link> if the <strong>itemprop</strong> attribute is present
* \<map> if it contains only phrasing content
* \<meta> if the <strong>itemprop</strong> attribute is present
 
I took the time to painstakingly write this out b/c holy cow! I thought I knew HTML, at least a little
bit.  But all of this?  Half of the tags I don't recognize.  And this info on phrasing content is
just the beginning: there are other [categories of content](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_categories)
as well: metadata content, flow content, sectioning content, heading content, embedded content, interactive content,
palpable content, and form-associated content.  The categories share some tags, but also have their own -- again, many of
which I have not used in the past.
 
So going back to the basics was a good idea!
 
### Now: More About Buttons!
So many attributes:
* autofocus
* autocomplete
* disabled
* form
* formaction
* formenctype
* formmethod
* formnovalidate
* formtarget
* name
* type
* value
