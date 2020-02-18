---
title: HTML5 and CSS Tips
date: 2017-01-26 08:34:12
tags: [HTML5, CSS]
categories: [Web前端]
---


## Elements
+ You can start a comment with ``<!--`` and end a comment with ``-->``
+ `CSS`: Note that it's important to have both opening and closing curly braces (`{` and `}`) around each element's style. You also need to make sure your element's style is between the opening and closing style tags. Finally, be sure to add the semicolon to the end of each of your element's styles.
```HTML
<style>
  h2 {color: red;}
</style>
```
+ `CSS clss`: Classes are reusable styles that can be added to HTML elements.Here is an example CSS class declaration:
```HTML
<style>
  .blue-text {
    color: blue;
  }
</style>
```
  You can apply a class to an HTML element like this:
```html
<h2 class="blue-text">CatPhotoApp</h2>
```
+ Import a Google Font:
```HTML
<link href="https://fonts.googleapis.com/css?family=Lobster" rel="stylesheet" type="text/css">
```
+ IMG: All `img` elements **must** have an alt attribute. The text inside an alt attribute is used for screen readers to improve accessibility and is displayed if the image fails to load.
```HTML
<img src="https://www.your-image-source.com/your-image.jpg" alt="Author standing on a beach with two thumbs up. ">
```
+ Clsss: Remember that you can apply multiple classes to an element by separating each class with a space within its class attribute. For example:
`<img class="class1 class2">`
+ Link to External Pages with Anchor Elements:
```HTML
<p>Here's a <a href="http://freecodecamp.com"> link to Free Code Camp</a> for you to follow.</p>
```
  `Nesting` just means putting one element inside of another element.
+ In addition to pixels, you can also specify a `border-radius` using a percentage. For example, 50% makes a circle.
+ Dead Links: Sometimes you want to add `a` elements to your website before you know where they will link. Replace the value of your `a` element's `href` attribute with a `#`, also known as a hash symbol, to turn it into a dead link.
```HTML
<p>Click here for <a href="#">cat photos</a>.</p>
```
+ Turn an Image into a Link: You can make elements into links by nesting them **within** an `a` element.
```HTML
<a href="#"><img src="https://bit.ly/fcc-running-cats" alt="Three kittens running towards the camera. "></a>
```
+ Unordered lists start with a `<ul>` element. Then they contain some number of `<li>` elements.
  Ordered lists start with a `<ol>` element. Then they contain some number of `<li>` elements.
```HTML
<ul>
  <li>milk</li>
  <li>cheese</li>
</ul>

<ol>
  <li>Garfield</li>
  <li>Sylvester</li>
</ol>
```
+  Text Field: Text inputs are a convenient way to get `input` from your user. Note that `input` elements are self-closing.
```HTML
<input type="text">
```
  If you wanted to make a text input field required, you can just add the word `required` within your `input` element, you would use:
  ```HTML
  <input type="text" required>
  ```
+ Add Placeholder Text to a Text Field: Your placeholder text is what appears in your text `input` before your user has input anything.
```HTML
<input type="text" placeholder="this is placeholder text">
```
+ **Form Element**: Form 对象代表一个 HTML 表单。在 HTML 文档中 `<form>` 每出现一次，Form 对象就会被创建。
表单用户通常用于收集用户数据，包含了 `input` 元素如：文本字段，复选框，单选框，提交按钮等。表单用于向服务端发送数据。

  You can build web forms that actually submit data to a server using nothing more than pure HTML. You can do this by specifying an action on your `form` element.
```HTML
<form action="/url-where-you-want-to-submit-form-data"></form>
<form action="/submit-cat-photo">
  <input type="text" placeholder="cat photo URL">
  <button type="submit">Submit</button>
</form>
```
+ Button:
```HTML
<button type="submit">this button submits the form</button>
```
+ **Radio Buttons**: Radio buttons are a type of `input`. Each of your radio buttons should be nested within **its own** `label` element. All related radio buttons should have the same `name` attribute.
```HTML
<label>  <input type="radio" name="indoor-outdoor">Indoor</label>
<label>  <input type="radio" name="indoor-outdoor">Outdoor</label>
```
+ Checkbox: Checkboxes are a type of `input`, Each of your checkboxes should be nested within its own `label` element. All related checkbox inputs should have the same `name` attribute.
```HTML
  <label><input type="checkbox" name="personality">One</label>
  <label><input type="checkbox" name="personality">Two</label>
  <label><input type="checkbox" name="personality">Three</label>
```
+ Check Radio Buttons and Checkboxes by Default: You can set a checkbox or radio button to be checked by default using the `checked` attribute.
```HTML
<input type="radio" name="test-name" checked>
```
+ **Div**: The `div` element is probably the most commonly used HTML element of all. It's useful for passing the CSS of its own class declarations down to all the elements that it contains.
+ **Element ID**: In addition to classes, each HTML element can also have an `id` attribute.
  There are several benefits to using `id` attributes, and you'll learn more about them once you start using jQuery.
  `id` attributes **should be unique**. Browsers won't enforce this, but it is a widely agreed upon best practice. So please **don't** give more than one element the same `id` attribute.
+ Use an ID Attribute to Style an Element:
  One cool thing about id attributes is that, like classes, you can style them using CSS. Exp:
  ```HTML
  <style>
    #cat-photo-element {
      background-color: green;
    }
  </style>
  ```
  Note that inside your `style` element, you always reference classes by putting a `.` in front of their names. You always reference ids by putting a `#` in front of their names.


## Style
+ Three important properties control the space that surrounds each HTML element: `padding`, `margin`, and `border`.
+ Padding: An element's `padding` controls the amount of space between the element and its `border`.

  CSS allows you to control the `padding` of an element on all four sides with `padding-top`, `padding-right`, `padding-bottom`, and `padding-left` properties.
+ Margin: An element's `margin` controls the amount of space between an element's `border` and surrounding elements.
  ![margin_padding](/sourcepictures/2017/01/26/margin_padding.png)
  If you set an element's margin to a negative value, the element will grow larger.

  CSS allows you to control the `margin` of an element on all four sides with `margin-top`, `margin-right`, `margin-bottom`, and `margin-left` properties.
+ Use **Clockwise Notation**(顺时针) to Specify the Padding of an Element

  Instead of specifying an element's `padding-top`, `padding-right`, `padding-bottom`, and `padding-left` properties, you can specify them all in one line, like this:`padding: 10px 20px 10px 20px;`

  These four values work like a clock: top, right, bottom, left, and will produce the exact same result as using the side-specific   padding instructions.

  ```html

    <style>
      .red-box {
        background-color: red;
        margin-top: 20px;
        margin-right:40px;
        margin-bottom:20px;
        margin-left:40px;
      }
    </style>

    ```

    可写为：

    ```html

    <style>
      .red-box {
        background-color: red;
        margin: 20px 40px 20px 40px;
      }
    </style>

  ```


  ## Inheritance

  + Remember, you can style your `body` element just like any other HTML element, and all your other elements will inherit your `body` element's styles.

    ```html
    <style>
      body {
        background-color: black;
        color: green;
        font-family:Monospace;
      }
    </style>

    <body>
      <h1>Hello World</h1>
    </body>

    ```
  + **Prioritize One Style Over Another**

    Sometimes your HTML elements will receive multiple styles that conflict with one another.

  + **Override Styles in Subsequent CSS**
    ```html
    <style>
      body {
        background-color: black;
        font-family: Monospace;
        color: green;
      }
      .pink-text {
        color: pink;
      }
      .blue-text {
        color: blue;
      }
    </style>
    <h1 class="pink-text blue-text">Hello World!</h1>
    ```
    **Note**: It doesn't matter which order the classes are listed in the HTML element.

    However, the order of the `class` declarations in the `<style>` section are what is important. The second declaration will always take precedence over the first. Because `.blue-text` is declared second, it overrides the attributes of `.pink-text`
  + **Override Class Declarations by Styling ID Attributes**
    ```HTML
    <style>
      body {
        background-color: black;
        font-family: Monospace;
        color: green;
      }
      .pink-text {
        color: pink;
      }
      .blue-text {
        color: blue;
      }
      #orange-text {
        color: orange;
      }
    </style>
    <h1 class="pink-text blue-text" id ="orange-text">Hello World!</h1>
    ```
    **Note**: It doesn't matter whether you declare this css above or below pink-text class, since id attribute will `always` take precedence.

    So we've proven that id declarations override class declarations, regardless of where they are declared in your `style` element CSS.
  + **Override Class Declarations with Inline Styles**

    Use an `in-line style` to try to make our `h1` element white. Remember, in line styles look like this:`<h1 style="color: green">`

    ```HTML
    <style>
      body {
        background-color: black;
        font-family: Monospace;
        color: green;
      }
      #orange-text {
        color: orange;
      }
      .pink-text {
        color: pink;
      }
      .blue-text {
        color: blue;
      }
    </style>
    <h1 id="orange-text" class="pink-text blue-text" style="color:white">Hello World!</h1>
    ```
  + **Override All Other Styles by using Important**

    Yay! We just proved that `in-line styles` will override all the CSS declarations in your style element.

    But wait. There's one last way to override CSS. This is the most powerful method of all.In many situations, you will use CSS libraries. These may accidentally override your own CSS. So when you absolutely need to be sure that an element has specific CSS, you can use `!important`.

    ```HTML
    <style>
      body {
        background-color: black;
        font-family: Monospace;
        color: green;
      }
      #orange-text {
        color: orange;
      }
      .pink-text {
        color: pink !important;
      }
      .blue-text {
        color: blue;
      }
    </style>
    <h1 id="orange-text" class="pink-text blue-text" style="color: white">Hello World!</h1>
    ```

  ## Colors
  + **Use Hex Code for specific Colors**

    Hex codes use 6 hexadecimal digits to represent colors, two each for red (R), green (G), and blue (B) components.
    For example, orange is pure red, mixed with some green, and no blue. In hex code, this translates to being `#FFA500`.

    Many people feel overwhelmed by the possibilities of more than 16 million colors. Fortunately, you can shorten it.
    For example, red's hex code `#FF0000` can be shortened to `#F00`. This reduces the total number of possible colors to around 4,000.
  + **Use RGB values to Color Elements**

    Another way you can represent colors in CSS is by using RGB values.
    The RGB value for black looks like this: `rgb(0, 0, 0)`.
    The RGB value for white looks like this: `rgb(255, 255, 255)`.
