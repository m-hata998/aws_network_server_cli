<!-- omit in toc -->
# ルートテーブルの関連付けの破棄

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. サブネット名

    SUBNET_NAME='public_subnet'

### 1.4. ルートテーブル名

    ROUTE_TABLE_NAME='public_route_table'

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

### 2.3. ルートテーブルID取得

    ROUTE_TABLE_ID=$( \
        aws ec2 describe-route-tables \
            --filters Name=tag:Name,Values=${ROUTE_TABLE_NAME}  \
            --query 'RouteTables[].RouteTableId' \
            --output text \
    ) && echo ${ROUTE_TABLE_ID}

### 2.4. 関連付けIDの取得

    ASSOCIATION_ID=$( \
        aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Associations[?SubnetId == \`${SUBNET_ID}\`].RouteTableAssociationId" \
        --output text \
    ) && echo ${ASSOCIATION_ID}

### 2.5. ルートテーブルとサブネットの関連付け削除

    aws ec2 disassociate-route-table \
        --association-id ${ASSOCIATION_ID}

## 3. 確認

### 3.1. 関連付けられたサブネットの確認

出力がないことを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Associations[?SubnetId == \`${SUBNET_ID}\`].RouteTableAssociationId" \
        --output text
