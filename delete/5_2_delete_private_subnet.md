<!-- omit in toc -->
# サブネットの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. サブネット名

    SUBNET_NAME='private_subnet'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. サブネットID取得

    SUBNET_ID=$( \
        aws ec2 describe-subnets \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=tag:Name,Values=${SUBNET_NAME}  \
            --query 'Subnets[].SubnetId' \
            --output text \
    ) && echo ${SUBNET_ID}

### 2.3. サブネット削除

    aws ec2 delete-subnet \
        --subnet-id ${SUBNET_ID}

## 3. 確認

### 3.1. サブネットの確認

出力がないことを確認

    aws ec2 describe-subnets \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=tag:Name,Values=${SUBNET_NAME}  \
        --query 'Subnets[].Tags[?Key == `Name`].Value' \
        --output text
