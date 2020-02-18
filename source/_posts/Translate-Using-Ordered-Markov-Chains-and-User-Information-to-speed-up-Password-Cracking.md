---
title: 翻译：使用有序马尔可夫链和用户信息加速密码破解速度
date: 2017-02-28 20:00:45
tags: [Translate]
categories: [Cryptography]
---

原文链接：[原文](http://fsecurify.com/using-ordered-markov-chains-and-user-information-to-speed-up-password-cracking/)

Using Ordered Markov Chains and User Information to speed up  Password Cracking
使用有序马尔可夫链和用户信息加速密码破解速度

I have been looking for ways to increase the speed of password cracking. There have been a lot of research done on the topic but there’s one team that has done some amazing work in this regard. There is a paper called OMEN: Faster Password Guessing Using an Ordered Markov Enumerator that was the inspiration for this post. The idea for this post was from this paper but all the work was manually done by me and no code or anything else have been copied. So, lets start.

我一直在寻找加快密码破解的速度的方法。有很多关于这个话题的研究，但是有一个团队在这方面已经做了一些了不起的工作。这里有一篇论文[OMEN: Faster Password Guessing Using an Ordered Markov Enumerator](https://hal.archives-ouvertes.fr/hal-01112124/file/omen.pdf)，就是我写这篇文章的灵感来源。虽然这篇文章的想法来自上面的论文，但是所有的工作都是我手动完成的，没有复制代码和其它任何东西。让我们开始吧！

# Using Ordered Markov Chains and User Information to speed up Password Cracking
使用有序马尔可夫链和用户信息加快密码破解速度
## Increasing cracking accuracy by 22.5% from John the ripper’s markov and incremental model
比John the ripper 的马尔可夫和增强模式提高22.5%的准确性

After reading the above paper, I wanted a similar tool but the tool made by those guys is not available anywhere. So, I spent a few days and wrote the code for creating such a tool that is flexible enough to perforn n-gram and markov chains based password generation using a cracked password list. I wanted to tinker with the tool and writing my own tool gave me freedom to do so. The tool is still in developmental phase. I’ll share it once it’s ready.

After writing the script, I wanted to see if we can leverage user information to speed up password cracking. There was a data breach for Fling, which is a dating/adult website. Their database was available publicly and I downloaded it and performed some analysis. Lets look into it.

阅读完上面提到的论文后，我想要一个类似的工具，但是这些家伙制作的工具已经不可用了。因此，我花了几天时间编写代码来实现这个工具，它足够灵活，使用一个破解的密码列表来实现基于`n-gram`和马尔可夫链的密码生成。我想修改并编写我自己的工具给了我这样做的自由。这个工具仍处于开发阶段，一旦完成，我将会把它分享出来。

编写脚本后，我想看看我们是否可以利用用户信息来加快密码破解速度。这里有一个Fling泄漏的数据，Fling是一个成人交友网站。他们泄漏的数据库可以公开获取到，我下载了这个数据并进行了一些分析。让我们来看看吧。

### Users using their personal information in their passwords
用户在其密码中使用个人信息

With a simple query, I was able to see that what percentage of users have the same first three letters in their passwords and emails/usernames/usercodes/nicknames etc. There are total 4993276 passwords in the fling database. Lets see the percentage of same starting trigrams.
通过一个简单的查询，我们可以看到用户密码与他们邮箱/用户名/用户代码/昵称有相同的前三个字母的用户占多大比例。在Fling泄漏的数据库中共有4993276个密码。让我们看看有相同起始三元组的占多大比例。

![trigram_fsecurify.jpg](/sourcepictures/2017/02/28/trigram_fsecurify.jpg)

8% of total passwords means almost 386,894 passwords. That’s a large number.
Lets look at 1-grams.
总密码的8% 意味着大约有386894个密码。这是一个很大的数字。
让我们看看`1-grams`。

![one_gram_fsecurify.jpg](/sourcepictures/2017/02/28/one_gram_fsecurify.jpg)

17% people have the same first letter in their password as they have in their email or username or nickname.

Let us a play a bit with birth dates and joining dates. I wanted to see that how many users use their birthdates at the end of their passwords. The query showed that 2% users do this. Here are a few results.
17%的用户的密码与其邮箱、用户名、昵称有相同的首字母。

让我们玩玩生日和加入网站日期。我想看看有多少用户在他们密码结尾使用他们的生日。查询显示2%的用户这样做。下面是一些结果。

![birthdates_fsecurify.jpg](/sourcepictures/2017/02/28/birthdates_fsecurify.jpg)

So, after just doing some very basic analysis, we have come to know that around 20% users use their very basic information in their passwords.

If this is not enough, I have got another dump (ClixSense) containing more user information like username, first_name, last_name, email, country, city, date of birth, date of joining, security_answer etc. I have crafted a query to see how many passwords have the same first n-grams that are also present in one of their social information. The results are astonishing. Lets have a look at them.

Using 2 grams and some more social information like date of birth, we see that 32.5% users use some of their social information in their passwords. Here’s the query.
在做了一些非常基础的分析之后，我们已经知道大约有20%的用户在他们密码中使用其非常基本的信息。

如果这还不够，我还有另一个泄漏的数据（ClixSense），包含更多的用户信息，例如用户名、名字、姓氏、电子邮件、国家、城市、生日、加入日期、安全问题等。我精心编写了一个查询，来看看有多少密码的前`n-grams`同样出现在他们的社交信息中。结果令人惊讶，让我们看看它们。

使用`2-grams`和更多的社交信息例如生日，我们发现32.5%的用户在密码中使用他们的社交信息。下面是这个查询：
```
select count(password) from users where lower(substring(password,1,2)) = lower(substring(username,1,2))
or substring(password,1,2) = substring(first_name,1,2)
or substring(password,1,2) = substring(email,1,2)
or substring(password,1,2) = substring(last_name,1,2)
or substring(password,1,2) = substring(city,1,2)
or substring(password,1,2) = substring(security_answer,1,2)
or substring(password,1,2) = substring(address1,1,2)
or substring(password,1,2) = substring(security_answer,1,2)
or substring(password,1,2) = substring(zip,1,2)
or substring(password,1,2) = substring(payable_to,1,2)
or lower(substring(password,1,2)) = lower(substring(country,1,2))
or substring(password,length(password)-3,4) = substring(date_of_birth,1,4)
or substring(password,length(password)-4,4) = substring(date_of_birth,1,4)
or substring(password,length(password)-3,4) = substring(register_date,1,4)
or substring(password,length(password)-3,2) = substring(username,length(username)-3,2)
or substring(password,length(password)-2,1) = substring(username,length(username)-2,1)
```

The result is 6.5 lac passwords out of 2 million passwords.

Can we leverage this to speed up password cracking? Yes, we can. Let’s dive into markov chains
结果是在200万密码中有65万密码使用社交信息。

我们可以使用这个来加速密码破解吗？是的，我们可以。让我们深入了解马尔可夫链（Markov Chain）。


### Markov Chains 马尔可夫链

To put in simple terms, what Markov chains do is that they tell us the probability of a letter occurring after a n gram. Suppose, we have a 4 gram ‘ilov’, the Markov chain would just tell us the probabilities of the next letters. In this example, most of the time, the highest probability of next letter is ‘e’ making it ‘ilove’. That’s all there is to Markov chains that you need to know. Given a n gram, we have the probabilities of next letters. If we sort these probabilities, we get passwords in the order of highest to lowest probabilities hence the name ordered markov chains.

John the ripper uses un-ordered Markov chains in a sense that it goes through one n gram completely before going to the next ngram. What my script did was go through all high probability n grams simultaneously using threads. I hope it’s clear, if it’s not, please feel free to ask anything in comments. You can also read the OMEN paper given in the start of this post.
简单来说，马尔可夫链所做的是，告诉我们一个字母在一个`n-gram`之后出现的概率。假设，我们有一个`4-gram` 'ilov'，马尔可夫链只会告诉我们下个字母出现的概率。在这个例子中，大部分时间，出现最高概率的字母是'e'，使它成为'ilove'。这就是你需要知道的关于马尔可夫链的所有知识。如果我们对这些概率进行排序，我们按照从最高到最低概率的顺序排列密码，故名有序马尔可夫链（ordered markov chains）。

John the ripper 使用未排序的马尔可夫链，从某种意义上来说，它完全遍历一个'n-grams'之后才到下一个'n-grams'。我的脚本的做法是，使用线程同时遍历所有高概率的'n-grams'。我希望已经解释清楚，如果没有的话，请在评论中提出任何问题。你也可以阅读文章开头给出的OMEN论文。

![the_end_2.png](/sourcepictures/2017/02/28/the_end_2.png)

### Cracking Passwords 破解密码
I will share the script for doing all of this stuff once it is complete. For now, I am just posting the results and the data.
一旦脚本完成，我将会分享这个处理数据的脚本。现在，我只是把结果和数据公布出来。

Dataset

The data I used for cracking came from MuslimMatch database dump. The hashes were stored in MD5 and username, user_code,user_email and user_country was given in the database dump. I have ignored the country for now and used only the username, user code and user email. There were 77000 hashes to crack.
数据集
我破解所使用的数据来自于 MuslimMatch 数据库泄漏的数据。密码使用MD5加密存储，同时泄漏的数据库中还有用户名、用户代码、用户邮箱和用户国家。我现在忽略了用户国家，只是使用用户名、用户代码和用户邮箱。大约有77000个哈希值需要破解。

John the ripper Incremental Mode
John the ripper 增强模式

I wanted to try 100 million passwords for each mode within length 6-12. First mode was incremental mode. The results were 15,000 hashes cracked for the first 100 million passwords. That is 20%
每种模式我想尝试1亿个长度在6-12位之间的密码。第一种模式是增强模式，结果是15000个哈希值被破解，占总数的20%。

John the ripper Markov Mode
John the ripper 马尔可夫模式

I also tried 100 million passwords here with level=210 and the same passwords lengths. The results were 20,000 hashes cracked for the first 100 million passwords. That is 25%.
这次我依然尝试了1亿个密码，同时设置`level=210`并保持密码长度不变。结果是有20000个哈希值被破解，占总数的25%。

Ordered Markov Chain with first three letters as n-grams from User’s information

What I did here was get tri-grams, tetra-grams, penta-grams and hexagrams from user information and used them as the starting letters. The following letters were used from a passwords list of mate1 that is available publicly. I used only 5 million passwords from the dump to show that this approach can perform better even with lesser passwords since John the ripper’s markov model is trained on rockyou passwords and they are around 13 million. This way, I was using a separate password list for training(mate1) and a separate one for testing(muslimmatch).

I generated 60 million passwords with n grams of user information approach and 40 million with simple ordered Markov chain approach with training list as mate1 passwords.

The results were 25,000 hashes cracked for the first 100 million passwords. That is around 32%, more than the incremental mode and markov mode.
用户信息的前三个字母作为`n-grams`的有序马尔可夫链

在这里，我从用户信息中获取 tri-grams, tetra-grams, penta-grams and hexagrams，并把它们作为密码的起始字母。接下来的字母使用了可以公开获取的[mate1](http://www.mate1.com/)密码列表。我只使用了500万个泄漏的密码数据，为了证明这种方法即使使用了更少的密码也可以执行的更好，因为John the ripper 的马尔可夫模型是基于1300万的rockyou密码训练而成的。这样，我使用一个的密码列表（mate1）进行训练，另一个不同的密码列表（muslimmatch）进行测试。

我使用`n-grams`用户信息方法生成了6000万个密码，使用简单的有序Markov链方法并使用mate1密码作为训练列表生成了4000万个密码。

结果是25000个哈希值被破解。 这大约是32％，多于增强模式和马尔可夫模式。


### Conclusion 结论

Why did this work? It worked because a large number of users use some part of their email or username or any other detail in their password and if we can first check passwords that start with n grams containing usernames portions, user emails portions etc, then we can intuitively speed up the accuracy and the experiments proved this intuition right. Another reason for increased accuracy was because I used my training word list from the same category as that of website i.e adult/relationship websites. These two factors were the main cause of the increase in accuracy.

Why does this matter? On my laptop, John the ripper generated 100 million passwords in less than 2 minutes. If we are cracking 32% passwords in less than 2 minutes, that’s a great thing. Isn’t it?

PS: I also did some experimentation with appending birth dates in the end and the results come out to be good.

That’s it. I hope this idea can be expanded further by you guys and we can see some amazing results. For privacy reasons, I cannot upload the pruned data of the dumps.

Note: This is by no means a statement that I have broken john the ripper’s speed and accuracy. That is a great tool made by some awesome people. I just think that using user’s social information as n grams with incremental markov models can increase the speed of password cracking to a good extent. Criticism is most welcome.
为什么它会发挥作用？它之所以起作用，是因为大量用户在密码中使用他们邮箱、用户名或其它详细信息；如果我们首先检查以部分用户名、用户邮箱开始的`n-grams`密码，我们可以直观的加快准确性，而这个实验证明了这种直觉是正确的。准确性提高的另一个原因是：我使用了相同类别的单词训练列表，都是成人社交网站。这两个因素是准确性提高的主要原因。

为什么这样做是有意义的？在我的笔记本电脑上，John the ripper 在不到2分钟时间内就产生了1亿个密码，如果我们能够在不到2分钟内破解32%的密码，这是很棒的事情，不是吗？

PS：我还做了一些试验，在密码结尾添加生日，而且结果是非常理想的。

这就是我所做的工作。我希望这个想法可以被你们进一步的拓展，让我们看到一些令人惊讶的结果。出于隐私的原因，我不能上传整理后的泄漏数据。

注意：这绝不意味着我已经打破了john the ripper的速度和准确性记录。John the ripper是一些了不起的人开发的一个伟大的工具。我只是认为，使用用户社交信息作为`n-grams`的增强马尔可夫模型可以显著提高密码破解速度。欢迎批评指正。

数据: https://github.com/faizann24/Using-Ordered-Markov-Chains-and-User-Information-to-Speed-up-Password-Cracking
