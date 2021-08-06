<!-- omit in toc -->
# Elastic IPの割当

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. Elastic IP名

    ELASTIC_IP_TAG_NAME='nat_gateway_address'

## 2. メイン処理

### 2.1. Elastic IPのタグ編集

    STRING_ELASTIC_IP_TAG="ResourceType=elastic-ip,Tags=[{Key=Name,Value=${ELASTIC_IP_TAG_NAME}}]" \
        && echo ${STRING_ELASTIC_IP_TAG}

### 2.2. Elastic IPの取得

    aws ec2 allocate-address \
        --tag-specifications ${STRING_ELASTIC_IP_TAG} 

## 3. 確認

### 3.1. Elastic IPの確認

Elastic Ip名[nat_gateway_address]が表示されることを確認

    aws ec2 describe-addresses \
        --filters Name=tag:Name,Values=${ELASTIC_IP_TAG_NAME}  \
        --query 'Addresses[].Tags[?Key == `Name`].Value' \
        --output text
