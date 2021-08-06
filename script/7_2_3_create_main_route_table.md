<!-- omit in toc -->
# NATゲートウェイのルートの追加

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. NATゲートウェイ名

    NAT_GATEWAY_NAME='nat_gateway'

### 1.4. 送信先CIDR

    DESTINATION_CIDR_BLOCK='0.0.0.0/0'

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

### 2.3. ルートテーブルID（メイン）取得

    ROUTE_TABLE_ID=$(
        aws ec2 describe-route-tables \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=association.main,Values=true \
            --query RouteTables[].RouteTableId \
            --output text
    ) && echo ${ROUTE_TABLE_ID}

### 2.4. ルートの作成

    aws ec2 create-route \
        --route-table-id ${ROUTE_TABLE_ID} \
        --nat-gateway-id ${NAT_GATEWAY_ID} \
        --destination-cidr-block ${DESTINATION_CIDR_BLOCK}

## 3. 確認

### 3.1. NATゲートウェイの確認

NATゲートウェイID[nat-xxxxxxxxxxxxxxxxx]が表示されることを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?NatGatewayId == \`${NAT_GATEWAY_ID}\`].NatGatewayId" \
        --output text

### 3.2. CIDRの確認

接続先CIDR[0.0.0.0/0]が表示されることを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?NatGatewayId == \`${NAT_GATEWAY_ID}\`].DestinationCidrBlock" \
        --output text
