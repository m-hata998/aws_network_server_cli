<!-- omit in toc -->
# NATゲートウェイの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. サブネット名

    SUBNET_NAME='public_subnet'

### 1.4. NATゲートウェイ名

    NAT_GATEWAY_NAME='nat_gateway'

### 1.5. 接続タイプ

    CONNECTIVITY_TYPE='public'

### 1.6. Elastic IP名

    ELASTIC_IP_TAG_NAME='nat_gateway_address'

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

### 2.3. Allocation ID取得

    ALLOCATION_ID=$( \
        aws ec2 describe-addresses \
            --filters Name=tag:Name,Values=${ELASTIC_IP_TAG_NAME}  \
            --query 'Addresses[].AllocationId' \
            --output text \
    ) && echo ${ALLOCATION_ID}

### 2.4. NATのタグ編集

    STRING_NAT_GATEWAY_TAG="ResourceType=natgateway,Tags=[{Key=Name,Value=${NAT_GATEWAY_NAME}}]" \
        && echo ${NAT_GATEWAY_NAME}

### 2.5. NATゲートウェイの作成

    aws ec2 create-nat-gateway \
        --allocation-id ${ALLOCATION_ID} \
        --subnet-id ${SUBNET_ID} \
        --tag-specifications ${STRING_NAT_GATEWAY_TAG} \
        --connectivity-type ${CONNECTIVITY_TYPE} 

## 3. 確認

### 3.1. NATゲートウェイの確認

NATゲートウェイ名[nat_gateway]が表示されることを確認

    aws ec2 describe-nat-gateways \
        --filter Name=vpc-id,Values=${VPC_ID} \
            Name=tag-key,Values=Name \
            Name=tag-value,Values=${NAT_GATEWAY_NAME} \
        --query 'NatGateways[].Tags[?Key == `Name`].Value' \
        --output text

### 3.2. 接続タイプの確認

接続タイプ[public]が表示されることを確認

    aws ec2 describe-nat-gateways \
        --filter Name=vpc-id,Values=${VPC_ID} \
            Name=tag-key,Values=Name \
            Name=tag-value,Values=${NAT_GATEWAY_NAME} \
        --query 'NatGateways[].ConnectivityType' \
        --output text
