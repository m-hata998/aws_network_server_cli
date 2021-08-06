<!-- omit in toc -->
# Elastic IPの解放

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. Elastic IP名

    ELASTIC_IP_TAG_NAME='nat_gateway_address'

## 2. メイン処理

### 2.1. Allocation ID取得

    ALLOCATION_ID=$( \
        aws ec2 describe-addresses \
            --filters Name=tag:Name,Values=${ELASTIC_IP_TAG_NAME}  \
            --query 'Addresses[].AllocationId' \
            --output text \
    ) && echo ${ALLOCATION_ID}

### 2.2. Elastic IPの取得

    aws ec2 release-address \
        --allocation-id ${ALLOCATION_ID} 

## 3. 確認

### 3.1. Elastic IPの確認

出力がないことを確認

    aws ec2 describe-addresses \
        --filters Name=tag:Name,Values=${ELASTIC_IP_TAG_NAME}  \
        --query 'Addresses[].Tags[?Key == `Name`].Value' \
        --output text
