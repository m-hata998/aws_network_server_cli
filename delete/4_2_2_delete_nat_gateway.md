<!-- omit in toc -->
# NATゲートウェイの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. NATゲートウェイ名

    NAT_GATEWAY_NAME='nat_gateway'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. NATゲートウェイ ID取得

    NAT_GATEWAY_ID=$( \
        aws ec2 describe-nat-gateways \
            --filter Name=vpc-id,Values=${VPC_ID} \
                Name=tag-key,Values=Name \
                Name=tag-value,Values=${NAT_GATEWAY_NAME} \
            --query NatGateways[].NatGatewayId \
            --output text \
    ) && echo ${NAT_GATEWAY_ID}

### 2.3. NATゲートウェイの作成

    aws ec2 delete-nat-gateway \
        --nat-gateway-id ${NAT_GATEWAY_ID}

## 3. 確認

### 3.1. NATゲートウェイの状態確認

[deleted]が出力されたらOK。  
[deleting]が出力される場合は時間をおいて再確認。

    aws ec2 describe-nat-gateways \
        --filter Name=vpc-id,Values=${VPC_ID} \
            Name=tag-key,Values=Name \
            Name=tag-value,Values=${NAT_GATEWAY_NAME} \
        --query 'NatGateways[].State' \
        --output text
