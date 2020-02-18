---
title: Scrapy爬取并保存GIF图片
date: 2017-09-18 09:32:20
tags: [Python, Scrapy]
categories: [Python]
---

在写爬虫的时候遇到要保存gif图片，发现Scrapy并没有提供这个功能，查阅了一些gif资料最后添加成功。

这里提供一段网上的代码，对我帮助很大，好像来自Google Group 忘记了，致谢！

```Python
class MyImagesPipeline(ImagesPipeline):

    def check_gif(self, image):
        if image.format == 'GIF':
            return True
        # The library reads GIF87a and GIF89a versions of the GIF file format.
        return image.info.get('version') in ['GIF89a', 'GIF87a']

    def persist_gif(self, key, data, info):
        root, ext = os.path.splitext(key)
        key = key + '.gif'
        absolute_path = self.store._get_filesystem_path(key)
        self.store._mkdir(os.path.dirname(absolute_path), info)
        f = open(absolute_path, 'wb')   # use 'b' to write binary data.
        f.write(data)

    def image_downloaded(self, response, request, info):
        checksum = None
        for key, image, buf in self.get_images(response, request, info):
            if checksum is None:
                buf.seek(0)
                checksum = md5sum(buf)
            if key.startswith('full') and self.check_gif(image):
                # Save gif from response directly.
                self.persist_gif(key, response.body, info)
            else:
                self.store.persist_image(key, image, buf, info)
        return checksum
```

我的方法与这个代码基本相同，只不过我没有写`persist_gif`函数，修改了`get_images`函数，这里就不贴出来我那丑陋的代码了。。。


最后列出一些gif知识：

```Python
#orig_image = Image.open('3.jpg')
orig_image = Image.open('1.gif')
print orig_image.info
#print dir(orig_image)
print vars(orig_image)
print orig_image.size

print 'format:', orig_image.format
print 'mode:', orig_image.mode

print orig_image.info['version'] in ['GIF89a', 'GIF87a']
```
