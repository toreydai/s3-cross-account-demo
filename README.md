# 中国区Amazon S3跨账号权限设置

### 1. 在账号A中创建一个S3存储桶
### 2. 在账号B中创建一个IAM用户或角色
### 3. 账号B中的IAM用户或角色添加IAM策略
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws-cn:s3:::AccountABucketName",
                "arn:aws-cn:s3:::AccountABucketName/*"
            ]
        }
    ]
}
```
### 4. 账号A的存储桶添加桶策略
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws-cn:iam::AccountB:user/AccountBUserName"
            },
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws-cn:s3:::AccountABucketName",
                "arn:aws-cn:s3:::AccountABucketName/*"
            ]
        }
    ]
}
```
### 5. 使用账号B的IAM用户或角色测试
```
# 配置账号B的IAM用户凭证
aws configure --profile cross-account
# 查看当前用户
aws sts get-caller-identity --profile cross-account

# 查看S3存储桶中的文件
aws s3 ls s3://cross-account-demo --profile cross-account

# 上传文件
aws s3 cp pv-volume.yaml s3://cross-account-demo --profile cross-account

# 下载文件
aws s3 cp s3://cross-account-demo/01_EC2_Instance.yaml . --profile cross-account
```
