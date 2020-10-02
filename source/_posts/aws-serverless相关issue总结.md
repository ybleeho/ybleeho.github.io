---
title: aws中国区serverless相关issue总结
date: 2020-04-11 08:46:29
tags:
---

1. 关于`sam local start-api`很慢的问题
   
   解决办法：不用每次pull image， 可以 [skip pull image](https://github.com/awslabs/aws-sam-cli/issues/134#issuecomment-390231341) `sam local start-api --skip-pull-image`。
   
   诶， 但mounting还是很慢，有没有[keep docker container warm]([https://github.com/awslabs/aws-sam-cli/issues/239](https://github.com/awslabs/aws-sam-cli/issues/239))的方法，看起来目前issue还是open状态，sam还不支持 keep warm。

2. 关于sam local start-api 的 custom authorizer不能调试问题
   
   解决办法：目前来看sam local还不支持这个功能，但可以[使用swagger来支持local的custom authorizers](https://github.com/awslabs/aws-sam-cli/issues/137#issuecomment-380022819)，但使用swagger又会使template bloated，但sam看起来过不久会[支持这个功能](https://github.com/awslabs/aws-sam-cli/issues/137#issuecomment-574256770)。

3. 每次修改代码sam build后才可以local start？
   
   解决办法：当代码有小改动或者调试时，可以直接在sam build后的`.aws-sam/build/`的输出文件下面改，此改动会立即生效，不用每次一次小改动后sam build在进行调试。当然这种方法也是有些危险的。有时候忘了把更改在build文件里面的内容复制到source文件进行代替，那么在重新build后原来的build里的更改会被替换掉。当然这种方法在代码的小改动后的调试啊什么的还是非常便捷的。

4. 中国区使用Zappa+Flask
   
   问题：zappa是serverless python开发的神器，比使用sam来进行开发，其便捷性和效率提高了不少。但zappa目前还没有[支持中国区的开发](https://github.com/Miserlou/Zappa/issues/1977#issue-543761465)，虽然尝试了其里面[issue](https://github.com/Miserlou/Zappa/issues/1564#issuecomment-457826900)的一些方法更改core文件，虽然部署成功，但因为中国区的功能上有很多阉割的，所以还是会有一系列问题，更改core文件的代码，看起来不太可靠安全。而且Zappa好像还没有支持中国区的打算，可能此路走不通。

5. 未完待续
   
   
   
