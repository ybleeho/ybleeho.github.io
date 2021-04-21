---
title: RoR 搭建web应用
date: 2020-04-23 22:02:38
tags:
---

### Installing Rails

1. mac 环境默认装有ruby ，`ruby -v` 查看版本 

2. 替换gem源 
   
   ```shell
   gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
   ```

3. 安装Rails
   
   ```shell
   sudo gem install rails
   ```

4. 测试安装是否正确
   
   ```shell
   ❯ rails -v
   Rails 6.0.2.2
   ```

### Createing Application

```shell
rails new blog
```

替换Gemfile的 source为 `source 'https://gems.ruby-china.com'` 然后在`bundle install`

注：Since Rails 6, Webpacker is the default JavaScript compiler. So you'll also have to set it up before starting your Rails server.

1. Install `yarn` using your OS package manager, or take a look at    [https://yarnpkg.com/en/docs/install](https://yarnpkg.com/en/docs/install)
2. Set it up `rails webpacker:install`
3. Make sure all packages are up to date `yarn install --check-files`
4. Start your Rails server `rails s`



## Node.js pros and cons

| Node.js pros                                                                                                                                                                                                                                         | Node.js cons                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| It represents the “JavaScript everywhere” paradigm<br/>It’s fast<br/>It’s lightweight<br/>It’s scalable<br/>Easy working with large files<br/>It handles multiple requests simultaneously<br/>It is growing really fast<br/>Good community around it | Low maturity<br/>No multithreading<br/>Can be rather unstable |

## Ruby on Rails pros and cons

| Ruby on Rails pros                                                                                                                                                       | Ruby on Rails cons                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Speed of development<br/>Simple, English-like syntax<br/>Good community around it<br/>Open-source solution with a rich set of gems<br/>**Convention Over Configuration** | Low performance<br/>Low scalability<br/>Slow runtime speed<br/>Lack of flexibility<br/>Open-source solution with a (too) rich set of gems |


