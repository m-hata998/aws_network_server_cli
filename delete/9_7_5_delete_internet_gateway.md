<!-- omit in toc -->
# インターネットゲートウェイの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. インターネットゲートウェイ名

    INET_GW_NAME='internet_gateway'

## 2. メイン処理

### 2.1. インターネットゲートウェイID取得

    INET_GW_ID=$( \
        aws ec2 describe-internet-gateways \
            --filters Name=tag:Name,Values=${INET_GW_NAME}  \
            --query 'InternetGateways[].InternetGatewayId' \
            --output text \
    ) && echo ${INET_GW_ID}

### 2.2. インターネットゲートウェイ削除

    aws ec2 delete-internet-gateway \
        --internet-gateway-id ${INET_GW_ID}

## 3. 確認

### 3.1. インターネットゲートウェイの確認

出力がなければOK

    aws ec2 describe-internet-gateways \
        --filters Name=tag:Name,Values=${INET_GW_NAME}  \
        --query 'InternetGateways[].Tags[?Key == `Name`].Value' \
        --output text
