<!-- omit in toc -->
# インターネットゲートウェイの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. インターネットゲートウェイ名

    INET_GW_NAME='internet_gateway'

## 2. メイン処理

### 2.1. インターネットゲートウェイのタグ編集

    STRING_INET_GW_TAG="ResourceType=internet-gateway,Tags=[{Key=Name,Value=${INET_GW_NAME}}]" \
        && echo ${STRING_INET_GW_TAG}

### 2.2. サブネット作成

    aws ec2 create-internet-gateway \
        --tag-specifications ${STRING_INET_GW_TAG}

## 3. 確認

### 3.1. サブネットの確認

サブネット名[internet_gateway]が表示されることを確認

    aws ec2 describe-internet-gateways \
        --filters Name=tag:Name,Values=${INET_GW_NAME}  \
        --query 'InternetGateways[].Tags[?Key == `Name`].Value' \
        --output text
