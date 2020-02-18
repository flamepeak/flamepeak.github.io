---
title: Bootstrap and Responive Design
date: 2017-01-31 08:56:54
tags: [Bootstrap, JavaScript, HTML]
categories: [Web前端]
---

[Bootstrap中文网:Bootstrap相关优质项目推荐](http://www.bootcss.com/)
[Bootstrap编码规范:编写灵活、稳定、高质量的 HTML 和 CSS 代码的规范。](http://codeguide.bootcss.com/)
[Bootstrap全局CSS样式](http://v3.bootcss.com/css/)

## Bootstrap responsive CSS framework
+ **Responsive Design with Bootstrap Fluid Containers**
Bootstrap will figure out how wide your screen is and respond by resizing your HTML elements - hence the name `Responsive Design`.
With responsive design, there is no need to design a mobile version of your website. It will look good on devices with screens of any width.

  You can add Bootstrap to any app by adding the following code to the top of your HTML:
  ```HTML
  <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.1/css/bootstrap.min.css"/>
  ```

  To get started, we should nest all of our HTML in a `div` element with the class `container-fluid`.
+ When we add an image to html, with Bootstrap, all we need to do is add the `img-responsive` class to your image. It will fit width of your page perfectly.
+ **Center Text with Bootstrap**
With Bootstrap, to center our heading, all we need to do is add the class `text-center` to our `h1` or `h2` element.
 `<h2 class="red-text text-center">CatPhotoApp</h2>`
+ **Create a Bootstrap Button**
  Bootstrap has its own styles for `button` elements, which look much better than the plain HTML ones. Give it the class `btn`.

  By making them block elements with the additional class of `btn-block`, your button will stretch to fill your page's entire horizontal space and any elements following it will flow onto a "new line" below the block.
  ```HTML
  <button class="btn">Submit</button>
  <button class="btn btn-block">Submit</button>
  ```
  Note that these buttons still need the `btn` class.
+ **btn-primary btn-info**
  Bootstrap comes with several pre-defined colors for buttons.
  `btn-primary`: It is useful for highlighting actions you want your user to take.
  `btn-info` :The `btn-info` class is used to call attention to optional actions that the user can take.
  `btn-danger` :The `btn-danger` class is the button color you'll use to notify users that the button performs a destructive action, such as deleting a photo.
  ```HTML
  <button class="btn btn-block btn-primary">Like</button>
  <button class="btn btn-block btn-info">Info</button>
  <button class="btn btn-block btn-danger">Delete</button>
  ```
  ![bootstrap_button.png](/sourcepictures/2017/01/31/bootstrap_button.png)
+ **Bootstrap Grid**
  Bootstrap uses a responsive grid system, which makes it easy to put elements into rows and specify each element's relative width. Most of Bootstrap's classes can be applied to a `div` element.

  Here's a diagram of how Bootstrap's 12-column grid layout works:
  ![stacked_to_horizontal.png](/sourcepictures/2017/01/31/stacked_to_horizontal.png)

  Here, `md` means medium, and `*` is a number specifying how many columns wide the element should be. In this case, the column width of an element on a medium-sized screen, such as a laptop, is being specified.

  `col-xs-*` :where `xs` means extra small (like an extra-small mobile phone screen).
  ```HTML
  <div class="container-fluid">
    <div class="row">
      <div class="col-xs-8">
        <h2 class="text-primary text-center">CatPhotoApp</h2>
      </div>
      <div class="col-xs-4">
        <a href="#"><img class="img-responsive thick-green-border" src="https://bit.ly/fcc-relaxing-cat" alt="A cute orange cat lying on its back. "></a>
      </div>
    </div>
  </div>
  ```
+ **Difference between `inline` elments and `block-level` elements.**
  ![inline_block_level.png](/sourcepictures/2017/01/31/inline_block_level.png)
+ **Use Spans for Inline Elements**
  By using the `span` element, you can put several elements together, and even style different parts of the same element differently.
  ```HTML
  <p>Top 3 things cats <span class = "text-danger">hate:</span></p>
  ```
+ **Font Awesome Icons**
  Font Awesome is a convenient library of icons. These icons are vector graphics, stored in the `.svg` file format. These icons are treated just like fonts. You can specify their size using pixels, and they will assume the font size of their parent HTML elements.

  You can add Font Awesome to any app just by including it by adding the following code to the top of your HTML:
  ```HTML
  <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.5.0/css/font-awesome.min.css"/>
  ```

  The `i` element was originally used to make other elements italic, but is now commonly used for icons. You add the Font Awesome classes to the `i` element to turn it into an icon, for example:
  ```HTML
  <i class="fa fa-info-circle"></i>
  ```

  ```HTML
  <div class="row">
    <div class="col-xs-4">
      <button class="btn btn-block btn-primary">
        <i class="fa fa-thumbs-up">Like</i>
      </button>
    </div>
    <div class="col-xs-4">
      <button class="btn btn-block btn-info">
        <i class="fa fa-info-circle">Info</i>
      </button>
    </div>
    <div class="col-xs-4">
      <button class="btn btn-block btn-danger">
        <i class="fa fa-trash">Delete</i>
      </button>
    </div>  
  </div>
  ```
+ **Responsively Style Radio Buttons and CheckBoxes**
  You can use Bootstrap's `col-xs-*` classes on `form` elements, too! This way, our radio buttons will be evenly spread out across the page, regardless of how wide the screen resolution is.

  ```HTML
  <form action="/submit-cat-photo">
    <div class="row">
      <div class="col-xs-6">
        <label><input type="radio" name="indoor-outdoor"> Indoor</label>
      </div>
      <div class="col-xs-6">
        <label><input type="radio" name="indoor-outdoor"> Outdoor</label>
      </div>
    </div>

    <div class="row">
      <div class="col-xs-4">
        <label><input type="checkbox" name="personality"> Loving</label>
      </div>
      <div class="col-xs-4">
        <label><input type="checkbox" name="personality"> Lazy</label>
      </div>
      <div class="col-xs-4">
        <label><input type="checkbox" name="personality"> Crazy</label>
      </div>
    </div>
    <input type="text" class="form-control" placeholder="cat photo URL" required>
    <button type="submit" class="btn btn-primary">
      <i class="fa fa-paper-plane">Submit</i>
    </button>
  </form>
  ```
+ Bootstrap Well
  Bootstrap has a class called `well` that can create a visual sense of depth for your columns.
  Well是一种会引起内容凹陷显示或插图效果的容器 <div>。
  ```html
  <div class="well">您好，我在 Well 中！</div>
  ```
