#### Div Section
```html
<div>Block element</div>
```
#### Headings
```html
<h1>Page title</h1>
<h2>Subheading</h2>
<h3>Tertiary heading</h3>
<h4>Quaternary heading</h4>
```
#### Paragraph
```html
<p style="text-align: center;">text</p>
```
#### Image
```html
<img src="/demo.jpg" alt="description" height="48" width="100" />
```
#### Outbound Link
```html
<a href="https://htmlg.com/" target="_blank" rel="nofollow">Click here</a>
```
#### Mailto link
```html
<a href="mailto:me@ruwix.com?Subject=Hi%20mate" target="_top">Send Mail</a>
```
#### Inner anchor (jump on page)
```html
<a href="#footer">Jump to footnote</a>
<br />
<a name="footer"></a>Footnote content
```
#### Bold text
```html
<strong>Bold text</strong>
```
#### <em>Italic text</em>
```html
<em>Italic text</em>
```
#### Form
```html
form action="/create" method="post">
    Name: <input name="name" type="text" /> <br />
    Age: <input max="99" min="1" name="age" step="1" type="number" value="18" /> <br />
    <select name="gender">
        <option selected="selected" value="male">Male</option>
        <option value="female">Female</option>
    </select><br />
    <input checked="checked" name="newsletter" type="radio" value="daily" /> Daily <input name="newsletter" type="radio" value="weekly" /> Weekly<br />
    <textarea cols="20" name="comments" rows="5">Comment</textarea><br />
    <label><input name="terms" type="checkbox" value="tandc" />Accept terms</label> <br />
    <input type="submit" value="Submit" />
</form>
```
