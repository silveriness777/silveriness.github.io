---
layout: post
title:  Spark系列文章（二）:Spark运行环境构建
category: bigdata 
tags: 大数据 Spark mac IDEA
keywords: bigdata,Spark,Spark学习之路,Spark系列文章,Spark运行环境,Spark运行环境IDEA
---

## 构建Spark运行环境，在IDEA上利用maven进行构建。

### 一、创建maven工程 

![](https://static.studytime.xin/image/articles/4300E26C-A569-4C7C-8BAD-B9BFEE64D787.png)


![](https://static.studytime.xin/image/articles/8101A1E8-54A2-4A01-8B95-A31B2E71F507.png)


### 二、设置groupId和artifactId 

![](https://static.studytime.xin/image/articles/8AB93938-BF6D-47B8-ACA6-4B96E351FE3B.png)


### 三、设置工程目录 

![](https://static.studytime.xin/image/articles/B12A998D-9049-4D01-8294-E8A6E0FAAF85.png)


![](https://static.studytime.xin/image/articles/63158B00-3F85-4284-B0E1-0EFC788BFF1C.png)


### 四、构建完成


![](https://static.studytime.xin/image/articles/EA262C76-6C86-461B-8853-AC958740312F.png)



### 五、上传项目至github

```
cd spark-learning-example
git init
git remote add origin git@git.guahao-inc.com:baihe/test.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

出现以下问题: 主要原因是github中的README.md文件不在本地代码目录中

![](https://static.studytime.xin/image/articles/013C0D76-6766-42B5-809E-F7AA9E9C2BAA.png)


通过以下方法解决:

```
git pull --rebase origin master
git push -u origin master
```
![](https://static.studytime.xin/image/articles/FA2D238C-6E0D-4264-A677-84B09128FD5C.png)


### 六、GitHub项目
![](https://static.studytime.xin/image/articles/821ADC7A-6024-4E36-BE3D-6903DFF2C772.png)



