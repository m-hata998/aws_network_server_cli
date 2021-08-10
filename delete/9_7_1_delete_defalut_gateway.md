<!-- omit in toc -->
# デフォルトゲートウェイの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. ルートテーブル名

    ROUTE_TABLE_NAME='public_route_table'

### 1.3. ルートを設定するCIDR

    DEST_CIDR='0.0.0.0/0'

## 2. メイン処理

### 2.1. ルートテーブルID取得

    ROUTE_TABLE_ID=$( \
        aws ec2 describe-route-tables \
            --filters Name=tag:Name,Values=${ROUTE_TABLE_NAME}  \
            --query 'RouteTables[].RouteTableId' \
            --output text \
    ) && echo ${ROUTE_TABLE_ID}

### 2.2. ルートの削除

    aws ec2 delete-route \
        --route-table-id ${ROUTE_TABLE_ID} \
        --destination-cidr-block ${DEST_CIDR}

## 3. 確認

### 3.1. ルートの確認

出力がないことを確認

    aws ec2 describe-route-tables \
        --route-table-ids ${ROUTE_TABLE_ID} \
        --query "RouteTables[].Routes[?DestinationCidrBlock == \`${DEST_CIDR}\`].GatewayId" \
        --output text
