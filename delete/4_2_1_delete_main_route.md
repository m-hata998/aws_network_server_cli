<!-- omit in toc -->
# NATゲートウェイへのルートの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. 送信先CIDR

    DESTINATION_CIDR_BLOCK='0.0.0.0/0'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. ルートテーブルID（メイン）取得

    ROUTE_TABLE_ID=$(
        aws ec2 describe-route-tables \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=association.main,Values=true \
            --query RouteTables[].RouteTableId \
            --output text
    ) && echo ${ROUTE_TABLE_ID}

### 2.3. ルートの削除

    aws ec2 delete-route \
        --route-table-id ${ROUTE_TABLE_ID} \
        --destination-cidr-block ${DESTINATION_CIDR_BLOCK}

## 3. 確認

### 3.1. NATゲートウェイの確認

出力がないことを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?DestinationCidrBlock == \`${DESTINATION_CIDR_BLOCK}\`].NatGatewayId" \
        --output text

### 3.2. CIDRの確認

出力がないことを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?DestinationCidrBlock == \`${DESTINATION_CIDR_BLOCK}\`].NatGatewayId" \
        --output text
