
# HTML - Overview
<b>HTML</b> (HyperText Markup Language) describes the <i>structure</i> of
a website. It is a text document that you can produce using any text editor.

Basic HTML can include directives for formatting in a language
called Cascading Style Sheets (CSS) and programs for interaction
in a language called JavaScript. JavaScript is a programming
language that’s used to make the website dynamic and interactive
HTML holds the content of the website CSS specifies the formatting.

Browsers, such as Firefox and Chrome, interpret the HTML along with
any CSS and JavaScript to produce what we experience when we visit
a website.


# Basic HTML Structure and Tags

HTML documents contain elements surrounded by <b>tags</b> - text that starts
with a < symbol and ends with a > symbol. An example of a
tag is: `<img src="home.gif"/>`.

This particular tag will display the image held in the file home.gif.
These tags are the markup. It is through the use of tags that
hyperlinks, images, and other media are included in web pages.


## Simple tags:
* `<h1>` first heading largest size
* `<p>` Start a paragraph
* `<a>` anchor The `<a>` element, or anchor element, it used to create a
hyperlink to another webpage or another location within the same webpage

Tags typically the content you wish to apply that tag to in this fashion:
`<TAGNAME> CONTENT </TAGNAME>`

An HTML element begins with a starting tag, which is followed by the
element content and an ending tag. The ending tag includes a /
symbol followed by the element type, for example /head.
Elements can be nested within elements.

A standard HTML document looks like this:
```HTML
<!DOCTYPE html>
<html>
   <head>
      <title>Very simple example
      </title>
   </head>
   <body>
      This will appear as is.
   </body>
</html>
```

This document consists of the html element, indicated by the starting
tag `<html>` and ending with the closing tag: `</html>`.

This head element here contains one element: title.
Title will display on the web browser giving users clarity
on what the page is about.

[A very Simple Web Page](https://storage.cloud.google.com/uconnstamfordcs/first.html")

Most of the visible content delivered will be delivered within the body tags

The '<!DOCTYPE html>' declaration is used to inform a website visitor's
browser that the document being rendered  is an HTML document.
While not actually an HTML element itself, every HTML document should
being with a DOCTYPE declaration to be compliant with HTML standard


## [Headings](https://s3.amazonaws.com/uconnweb/headings.html)

Heading tags H1 to H6 scale down in size
```HTML
<H1> HEADING 1 </H1>    Large
<H2> HEADING 2 </H2>
<H3> HEADING 3 </H3>
<H4> HEADING 4 </H4>
<H5> HEADING 5 </H5>
<H6> HEADING 6 </H6>    Small
```


## some other simple tags

#### `<pre>` Creates preformatted text `</pre>`

<pre> Creates preformatted text </pre>

#### `<em>`Creates address section, usually processed in italics`</em>`

<em>Creates address section, usually processed in italics</em>

#### `<strong>` Emphasizes a word (usually processed in italics) `</strong>`

<strong> Emphasizes a word (usually processed in italics) </strong>

#### `<p>` Creates a new paragraph`</p>`

<p> Creates a new paragraph</p>

#### `<blockquote>` Puts content in a quote - indents text from both sides`</blockquote>`

<blockquote> Puts content in a quote - indents text from both sides</blockquote>

#### `<div>`Used to format block content with CSS `</div>`

<div>Used to format block content with CSS </div>

#### `<hr>` - Inserts a horizontal rule

<hr>

#### `<br>` - Inserts a line break (carriage return)

<br>


## Lists
Tag|Description
-----|-----
`<ul></ul>`|Unordered List
`<ol></ol>`|Ordered List
`<li></li>`|Encompasses each list item


### Unordered List
```HTML
<ul>
    <li>Encompasses each list item </li>
    <li>Next List Item </li>
</ul>
```
<ul>
    <li>Encompasses each list item </li>
    <li>Next List Item </li>
</ul>


### Ordered list
```HTML
<ol>
    <li>first</li>
    <li>second</li>
</ol>
```
<ol>
    <li>first</li>
    <li>second</li>
</ol>



## Links

We can link to url's anywhere in the internet:
```HTML
<a href="https://www.awseducate.com/">clickable text goes to url awseducate.com</a>
```

Or we can link to pages directly in our current directory:
```HTML
<a href="simple.html">clickable text goes to url</a>
```

Can create a link for an email address:
```HTML
<a href="mailto:EMAIL_ADDRESS">john.iacovacci1@gmail.com</a>
```

Also, you can link to different sections of a web page using a link to
#NAME, replacing NAME with the ID of an element on the page.

```HTML
<a name="NAME">
Creates a target location within a document
<a href="#NAME">clickable text</a>
Creates a link to that target location
```

## Images
Adds image; it is a separate file located at the URL

```HTML
<img src="https://uconnhuskies.com/images/logos/site/site.png">
```
<div align="center">
    <img src="https://uconnhuskies.com/images/logos/site/site.png">
</div>

## Forms

Forms are used to collect data inputted by a user. It is an
interface for a web application to send data across the web.
A <b>form</b> defines the form and within this tag,
an action attribute is needed to tell the form where its
contents will be sent to.

The <b>method</b> attribute tells the form how the data in will be sent

Method|Description
------|------
<b>get</b>|latches the form information onto the url as a query
<b>post</b>|sends the form’s information in JSON in the message body

The <b>action</b> attribute defines the url to send the form information
to.

Form Example:
```HTML
<form action="processingscript.php" method="post"></form>
```

This example will send the form information as a post to processingscript.php


### Inputs

`<input type="_______">`|Description
----------------------|-----------
`text` | is a standard textbox. This can also have a value attribute, setting initial text in the textbox.
`password` | The characters typed in by the user will be hidden.
`checkbox` | is a checkbox, which can be toggled on and off by the user. This can also have a checked attribute
`checkbox` | the attribute doesn’t  makes the initial state of the check box to be switched on, as it were.
`radio` | is similar to a checkbox, but the user can only select one radio button in a group. This can also have a checked attribute.
`submit` | is a button that will submit the form.

You can control the text that appears on the submit button with the value attribute, for example:
```HTML
<input type="submit" value="Click Me Now">
```


## Textarea
textarea is, basically, a large, multi-line textbox. The
anticipated number of rows and columns can be defined
with rows and cols attributes:
```HTML
<textarea rows="5" cols="20">A big load of text</textarea>
```


## Select

The select tag works with the option tag to make drop-down select boxes.
```
<select>
    <option>Option 1</option>
    <option>Option 2</option>
    <option value="third option">Option 3</option>
</select>
```

When the form is submitted, the value of the selected option will be sent


## Names
All of the tags mentioned above will look very nice presented on the
page but if you hook up your form to a form-handling script, they
will all be ignored. This is because the form fields need names.
So to all of the fields, the attribute name needs to be added,
for example `<input type="text" name="talkingsponge">`.

```HTML
<form action="contactus.php" method="post">

    <p>Name:</p>
    <p><input type="text" name="name" value="Your name"></p>

    <p>Species:</p>
    <p><input name="species"></p>
    <!-- remember: 'type="text"' isn't actually necessary -->

    <p>Comments: </p>
    <p><textarea name="comments" rows="5" cols="20">Your comments</textarea></p>

    <p>Are you:</p>
    <p><input type="radio" name="areyou" value="male">Male</p>
    <p><input type="radio" name="areyou" value="female">Female</p>
    <p><input type="radio" name="areyou" value="other">Other</p>

    <p><input type="submit"></p>

</form>
```


# Resources
* [W3 Schools](https://www.w3schools.com/html/default.asp)