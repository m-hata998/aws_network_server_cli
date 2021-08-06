<!-- omit in toc -->
# プライベートサブネットの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. サブネット名

    SUBNET_NAME='private_subnet'

### 1.4. サブネットCIDR

    SUBNET_CIDR='10.0.2.0/24'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. サブネットのタグ編集

    STRING_SUBNET_TAG="ResourceType=subnet,Tags=[{Key=Name,Value=${SUBNET_NAME}}]" \
        && echo ${STRING_SUBNET_TAG}

### 2.3. サブネット作成

    aws ec2 create-subnet \
        --vpc-id ${VPC_ID} \
        --cidr-block ${SUBNET_CIDR} \
        --tag-specifications ${STRING_SUBNET_TAG}

## 3. 確認

### 3.1. サブネットの確認

サブネット名[private_subnet]が表示されることを確認

    aws ec2 describe-subnets \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=tag:Name,Values=${SUBNET_NAME}  \
        --query 'Subnets[].Tags[?Key == `Name`].Value' \
        --output text

### 3.2. CIDRの確認

CIDR[10.0.2.0/24]が表示されることを確認

    aws ec2 describe-subnets \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=tag:Name,Values=${SUBNET_NAME}  \
        --query 'Subnets[].CidrBlock' \
        --output text
