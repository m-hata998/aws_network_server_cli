<!-- omit in toc -->
# EC2の作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. EC2の名称

    EC2_TAG_NAME='web_server'

### 1.3. プライベートIPアドレス

    EC2_PRIVATE_IP_ADDRESS='10.0.1.10'

### 1.4. AMIイメージ

    EC2_INSTANCE_IMAGE_NAME='amzn2-ami-hvm-2.0.????????.?-x86_64-gp2'

### 1.5. インスタンスタイプ

    EC2_INSTANCE_TYPE='t2.nano'

### 1.6. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.7. サブネット名

    SUBNET_NAME='public_subnet'

### 1.8. セキュリティグループの名称

    SECURIYT_GROUP_NAME='web_sg'

### 1.9. キーペア名

    KEY_PAIR='my-key'

## 2. メイン処理

### 2.1. AMIイメージID取得

    EC2_INSTANCE_IMAGE_ID=$( \
        aws ec2 describe-images \
            --filters Name=name,Values="${EC2_INSTANCE_IMAGE_NAME}" "Name=state,Values=available" \
            --query "reverse(sort_by(Images, &Name))[:1].ImageId" \
            --output text \
    ) && echo ${EC2_INSTANCE_IMAGE_ID}

### 2.2. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.3. サブネットID取得

    SUBNET_ID=$( \
        aws ec2 describe-subnets \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=tag:Name,Values=${SUBNET_NAME}  \
            --query 'Subnets[].SubnetId' \
            --output text \
    ) && echo ${SUBNET_ID}

### 2.4. セキュリティグループID取得

    SECURITY_GROUP_ID=$( \
        aws ec2 describe-security-groups \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=group-name,Values=${SECURIYT_GROUP_NAME} \
            --query SecurityGroups[].GroupId \
            --output text
    ) && echo ${SECURITY_GROUP_ID}

### 2.5. EC2のタグ編集

    STRING_EC2_TAG="ResourceType=instance,Tags=[{Key=Name,Value=${EC2_TAG_NAME}}]" \
        && echo ${STRING_EC2_TAG}

### 2.6. EC2作成

    aws ec2 run-instances \
        --image-id "${EC2_INSTANCE_IMAGE_ID}" \
        --instance-type ${EC2_INSTANCE_TYPE} \
        --tag-specifications ${STRING_EC2_TAG} \
        --subnet-id ${SUBNET_ID} \
        --security-group-ids ${SECURITY_GROUP_ID} \
        --key-name ${KEY_PAIR} \
        --private-ip-address "${EC2_PRIVATE_IP_ADDRESS}" \
        --associate-public-ip-address

## 3. 確認

### 3.1. インスタンスの存在確認

インスタンス名[web_server]が出力されたらOK

    aws ec2 describe-instances \
        --filters Name=tag-key,Values=Name \
                    Name=tag-value,Values=${EC2_TAG_NAME} \
                    Name=instance-state-name,Values=running \
        --query Reservations[].Instances[].Tags[].Value \
        --output text
