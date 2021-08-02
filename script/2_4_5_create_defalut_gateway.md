<!-- omit in toc -->
# デフォルトゲートウェイを設定

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. ルートテーブル名

    ROUTE_TABLE_NAME='public_route_table'

### 1.3. インターネットゲートウェイ名

    INET_GW_NAME='internet_gateway'

### 1.4. ルートを設定するCIDR

    DEST_CIDR='0.0.0.0/0'

## 2. メイン処理

### 2.1. インターネットゲートウェイID取得

    INET_GW_ID=$( \
        aws ec2 describe-internet-gateways \
            --filters Name=tag:Name,Values=${INET_GW_NAME}  \
            --query 'InternetGateways[].InternetGatewayId' \
            --output text \
    ) && echo ${INET_GW_ID}

### 2.2. ルートテーブルID取得

    ROUTE_TABLE_ID=$( \
        aws ec2 describe-route-tables \
            --filters Name=tag:Name,Values=${ROUTE_TABLE_NAME}  \
            --query 'RouteTables[].RouteTableId' \
            --output text \
    ) && echo ${ROUTE_TABLE_ID}

### 2.3. ルートの追加

    aws ec2 create-route \
        --route-table-id ${ROUTE_TABLE_ID} \
        --destination-cidr-block ${DEST_CIDR} \
        --gateway-id ${INET_GW_ID}

## 3. 確認

### 3.1. ルートの確認

CIDR[0.0.0.0/0]が表示されることを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?GatewayId == \`${INET_GW_ID}\`].DestinationCidrBlock" \
        --output text
