<!-- omit in toc -->
# DNSホスト名の有効化

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

### 2.2. DNSホスト名の有効化

    aws ec2 modify-vpc-attribute \
        --vpc-id ${VPC_ID}  \
        --enable-dns-hostnames

## 3. 確認

### 3.1. DNSホスト名有効化の確認

[true]が表示されることを確認

    aws ec2 describe-vpc-attribute \
        --vpc-id ${VPC_ID} \
        --attribute enableDnsHostnames \
        --query EnableDnsHostnames.Value
