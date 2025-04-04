# Jekyll+GithubPage

# 一、先安装环境（ubuntu）

## 1、安装ruby

```
sudo apt update
sudo apt install ruby-full build-essential zlib1g-dev
```

查看是否安装成功

```
ruby -v
```

## 2、使用RubyGems安装Jekyll

```
gem install jekyll bundler
```

（此处需要挂代理）

查看是否安装成功

```
jekyll -v
```

# 二、测试环境

创建一个测试的blog

```
jekyll new myblog
cd myblog
```

‍
