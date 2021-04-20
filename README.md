# serverless-framework教程
- 本教程运行环境:Ubuntu16.04
- 主要介绍AWS平台上serverless
### 安装serverless 
```bash
curl -o- -L https://slss.io/install | bash
#安装完成后，查看是否安装成功
serverless -v
```
### 设置serverless credentials
serverless config credentials [Options]

> --provider or -p 填aws 必填

> --key or -k aws_access_key_id 必填

> --secret or -s The aws_secret_access_key必填

> --profile or -n credentials配置文件，例如我在ubuntu系统上 ~/.aws/credentials文件

> --overwrite or -o 以当前配置覆盖~/.aws/credentials文件中的内容
```bash
#命令示例
serverless config credentials --provider aws --key xxxxxx --secret xxxxxx
```

### 初始化一个新项目serverless create
serverless create [Options]

> --template or -t aws自带模板名称，例如aws-nodejs

> --template-url or -u 第三方模板地址，例如github上的模板，例如https://github.com/xxx/aws-nodejs

> --template-path 本地你自己的模板路径，例如/usr/local/nginx/template/aws-nodejs

> --path or -p 改项目将要创建在哪个目录下，例如/usr/local/nginx/lambda/nodejs-lambda/

> --name or -n 项目名称，该名称将会被写入到当前项目的serverless.yml文件中

```bash
#命令示例
serverless create --template aws-nodejs --path nodejsLambda  --name nodejs-lambda 

#提示创建成功，会产生下面三个文件
.gitignore
handler.js 项目入口文件
serverless.yml 这个文件是项目自动化部署时，要用到的配置文件
```
### serverless package打包项目
serverless package  [Options]

> --stage or -s 将要部署到什么环境，例如production

> --region or -r 将要把项目部署到哪个区域，例如中国区cn-north-1
 
> --package or -p 你将要把项目打包到哪个目录下

```bash 
#执行完下命令后，会在打包路径下生成目录.serverless
serverless package --stage production --region cn-north-1
```

### serverless deploy部署项目
serverless deploy [Options]

> --config or -c 项目配置文件的文件名，例如serverless.yml|.yaml|.js|.json，默认serverless.yml

> --noDeploy or -n 不部署到aws,仅仅是把代码打包配置好，版本在./serverless目录中

> --stage or -s 将要部署到什么环境，例如production、dev

> --region or -r 将要把项目部署到哪个区域，例如中国区cn-north-1、us-east-1

> --package or -p 执行命令serverless package打包时候所指定的打包文件所在目录

> --verbose or -v 显示部署期间的所有信息

> --force 强制进行部署

> --function or -f 仅仅部署某个函数，和--package选项不能同时使用

> --conceal 不显示项目敏感信息，例如密钥信息

> --aws-s3-accelerate 打开S3传输，使得上传速度更快

> --no-aws-s3-accelerate 禁用S3传输

```bash
#命令示例
serverless deploy --stage production --region cn-north-1 #部署项目到aws
serverless deploy list 列出发布过的历史记录列表
serverless deploy list functions 列出发布过的函数列表
```
> 登录到AWS控制台，找到cloudformation和AWS lambda，可以看到上面部署的项目


### 实战

- 执行下面命令，创建package.json文件
```
mkdir -p layer/nodejs #必须将第三方模块安装在layer/nodejs文件夹下
cd layer/nodejs
npm init //一路回车
npm install -D md5 #安装第三方模块：md5.js
```

```

- 修改hander.js文件
 ```javascript
'use strict';
module.exports.hello = async (event) => {
    const md5 = require('md5');
    let name = "123456";
    let str = md5(name);
    return {
        statusCode: 200,
        body: JSON.stringify(
            {
                message: 'Go Serverless v1.0! Your function executed successfully!' + str,
                input: event,
            },
            null,
            2
        ),
    };
};
 ```

- 因安装了第三方模块md5，需求修改serverless.yml文件
默认serverless排除了下列文件及文件夹
```
.git/**
.gitignore
.DS_Store
npm-debug.log
.serverless/**
.serverless_plugins/**
```

### 修改serverless.yml文件
- 为省去每次部署打包时候都要带各种参数的麻烦，可以修改serverless.yml，对其中配置项进行配置
```
  stage: production #每次部署的默认环境，可以使production、dev或者其他
  region: cn-north-1 #国家区域
 
#对于安装的第三方模块，默认不会被打包上传，可以配置layer
layers:
  commonLibs:
    path: layer
    compatibleRuntimes:
      - nodejs12.x
      
functions:
  hello:
    handler: handler.hello
    layers:
      # Ref name is generated by TitleCasing the layer name & appending LambdaLayer
      - {Ref: CommonLibsLambdaLayer}
```
### invoke本地调试项目
避免每次调试都要发布，可以在本地调试，之后再进行部署
```
serverless invoke --function hello //本地调试调用hello函数
```

### 部署到aws

```
serverless deploy #由于在上面已经在serverless.yml中配置了stage，region，此处可以直接不带参数
```
 - 到aws控制台查看


### serverless logs
```
serverless logs -f hello #在aws控制台执行了lambda函数，此处会打印函数执行产生日志信息
serverless logs -f hello -t #-t 即tail，实时监控日志信息，当你在aws控制台执行了lambda函数，此处会实时打印函数执行时产生的日志信息
```

### serverless metrics
查看监控信息
```
serverless metrics #查看24小时内监控信息（默认查看24小时内）
serverless metrics --startTime 2021-01-01 --endTime 2021-08-02 #查看某个时间段内的监控信息
serverless metrics --function hello #查看hello函数24小时内监控信息（默认查看24小时内）
serverless metrics --function hello --startTime 2021-01-01 --endTime 2021-08-02 #查看某个时间段内hello函数的监控信息
```

### serverless info
查看项目信息
```
serverless info #查看项目信息
serverless info --stage dev #查看部署在dev环境上的项目信息
serverless info --region cn-north-1 #查看部署在cn-north-1上的项目信息
serverless info --verbose #查看部署项目的详细信息
```

### serverless rollback 
项目回滚
```
serverless deploy list #先查看部署历史版本记录
serverless rollback -t 1618840660250 #上面命令执行后，可以看到每条更新记录的Timestamp信息，将Timestamp信息填写到这里的-t即可
serverless rollback -t 1618840660250 --verbose #回滚时候输出详细日志信息
serverless rollback -t 1618840660250 -v #回滚时候输出详细日志信息
```


 
