# serverless-framework-tutorial
serverless-framework教程
本教程运行环境:Ubuntu16.04
主要介绍AWS平台上serverless
### 安装serverless 
```bash
curl -o- -L https://slss.io/install | bash
#安装完成后，查看是否安装成功
serverless -v
```
### 设置aws credentials
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
> serverless create [Options]

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


> serverless package  [Options]

> --stage or -s 将要部署到什么环境，例如production

> --region or -r 将要把项目部署到哪个区域，例如中国区cn-north-1
 
> --package or -p 你将要把项目打包到哪个目录下

```bash 
#执行完下命令后，会在打包路径下生成目录.serverless
serverless package --stage production --region cn-north-1
```

### serverless deploy部署项目
- 项目打包完成后，将本地代码部署到aws上

> --config or -c 项目配置文件的文件名，例如serverless.yml|.yaml|.js|.json，默认serverless.yml

> --noDeploy or -n 不部署到aws,仅仅是把代码打包配置好，版本在./serverless目录中

> --stage or -s 将要部署到什么环境，例如production

> --region or -r 将要把项目部署到哪个区域，例如中国区cn-north-1

> --package or -p 执行命令serverless package打包时候所指定的打包文件所在目录

> --verbose or -v 显示部署期间的所有信息

> --force 强制进行部署

> --function or -f 仅仅部署某个函数，和--package选项不能同时使用

> --conceal 不显示项目敏感信息，例如密钥信息

> --aws-s3-accelerate 打开S3传输，使得上传速度更快

> --no-aws-s3-accelerate 禁用S3传输

```bash
#命令示例
serverless deploy --stage production --region cn-north-1
