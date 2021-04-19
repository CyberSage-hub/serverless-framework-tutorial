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
--provider or -p 填aws 必填
--key or -k aws_access_key_id 必填
--secret or -s The aws_secret_access_key必填
--profile or -n credentials配置文件，例如我在ubuntu系统上 ~/.aws/credentials文件
--overwrite or -o 以当前配置覆盖~/.aws/credentials文件中的内容

```bash
serverless config credentials --provider aws --key xxxxxx --secret xxxxxx
```

