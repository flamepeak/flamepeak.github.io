---
title: Python PIL 缩略图 清晰度问题
date: 2017-08-22 10:12:27
tags: [Python, Scrapy]
categories: [Python]
---

在使用Scrapy 爬取文章时，把下载的文章存储为缩略图，在APP显示时出现图片质量差，模糊的问题。

查看Scrapy源代码，看到Scrapy图片缩略图生成使用的函数是thumbnail，

```Python
class ImagesPipeline(FilesPipeline):
    #此处省略。。。。。
    def convert_image(self, image, size=None):
        if image.format == 'PNG' and image.mode == 'RGBA':
            background = Image.new('RGBA', image.size, (255, 255, 255))
            background.paste(image, image)
            image = background.convert('RGB')
        elif image.mode == 'P':
            image = image.convert("RGBA")
            background = Image.new('RGBA', image.size, (255, 255, 255))
            background.paste(image, image)
            image = background.convert('RGB')
        elif image.mode != 'RGB':
            image = image.convert('RGB')

        if size:
            image = image.copy()
            image.thumbnail(size, Image.ANTIALIAS)

        buf = BytesIO()
        image.save(buf, 'JPEG')
        return image, buf
```

使用python的PIL库的thumbnail方法生成缩略图的质量很差，需要使用resize方法来生成缩略图，并指定缩略图质量，代码如下：

```Python
image = image.resize((x, y), Image.ANTIALIAS)

quality_val = 95
image.save(filename, 'JPEG', quality=quality_val)
```

这样生成图片的质量比直接使用thumbnail方法会好很多。

参考： [How do you create a custom image pipeline that can manipulate the images with PIL?](https://groups.google.com/forum/#!topic/scrapy-users/uPB6U4cOiz4)

附上Scrapy Pipeline代码：

```Python
#MyImagesPipeline #self
class MyImagesPipeline(ImagesPipeline):
    def convert_image(self, image, size=None):
        if image.format == 'PNG' and image.mode == 'RGBA':
            background = Image.new('RGBA', image.size, (255, 255, 255))
            background.paste(image, image)
            image = background.convert('RGB')
        elif image.mode == 'P':
            image = image.convert("RGBA")
            background = Image.new('RGBA', image.size, (255, 255, 255))
            background.paste(image, image)
            image = background.convert('RGB')
        elif image.mode != 'RGB':
            image = image.convert('RGB')

        if size:
            image = image.copy()
            #image.thumbnail(size, Image.ANTIALIAS)
            image = image.resize(size, Image.ANTIALIAS)

        buf = BytesIO()
        quality_val = 95
        image.save(buf, 'JPEG', quality=quality_val)
        return image, buf
```


