<!-- omit in toc -->
# VPCの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. IPv4 CIDRブロック

    VPC_CIDR='10.0.0.0/16'

## 2. メイン処理

### 2.1. VPCのタグ編集

    STRING_VPC_TAG="ResourceType=vpc,Tags=[{Key=Name,Value=${VPC_TAG_NAME}}]" \
        && echo ${STRING_VPC_TAG}

### 2.2. VPC作成

    aws ec2 create-vpc \
        --cidr-block ${VPC_CIDR} \
        --tag-specifications ${STRING_VPC_TAG}

## 3. 確認

### 3.1. VPCの確認

VPC名[vpc_area]が表示されることを確認

    aws ec2 describe-vpcs \
        --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
        --query 'Vpcs[].Tags[?Key == `Name`].Value' \
        --output text

### 3.2. CIDRの確認

CIDR[10.0.0.0/16]が表示されることを確認

    aws ec2 describe-vpcs \
        --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
        --query 'Vpcs[].CidrBlock' \
        --output text
