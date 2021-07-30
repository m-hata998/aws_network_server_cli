<!-- omit in toc -->
# インターネットゲートウェイをアタッチ

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. インターネットゲートウェイ名

    INET_GW_NAME='internet_gateway'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. インターネットゲートウェイID取得

    INET_GW_ID=$( \
        aws ec2 describe-internet-gateways \
            --filters Name=tag:Name,Values=${INET_GW_NAME}  \
            --query 'InternetGateways[].InternetGatewayId' \
            --output text \
    ) && echo ${INET_GW_ID}

### 2.3. VPCにインターネットゲートウェイをアタッチ

    aws ec2 attach-internet-gateway \
        --internet-gateway-id ${INET_GW_ID} \
        --vpc-id ${VPC_ID}

## 3. 確認

### 3.1. アタッチされたVPCの確認

VPCIDが表示されることを確認

    aws ec2 describe-internet-gateways \
        --filters Name=tag:Name,Values=${INET_GW_NAME}  \
        --query "InternetGateways[].Attachments[?VpcId == \`${VPC_ID}\`].VpcId" \
        --output text
