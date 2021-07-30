<!-- omit in toc -->
# VPCの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. VPC削除

    aws ec2 delete-vpc \
        --vpc-id ${VPC_ID}

## 3. 確認

### 3.1. VPCの確認

出力がなければOK

    aws ec2 describe-vpcs \
        --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
        --query 'Vpcs[].Tags[?Key == `Name`].Value' \
        --output text
