<!-- omit in toc -->
# ルートテーブルの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.3. ルートテーブル名

    ROUTE_TABLE_NAME='public_route_table'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. ルートテーブルのタグ編集

    STRING_ROUTE_TABLE_TAG="ResourceType=route-table,Tags=[{Key=Name,Value=${ROUTE_TABLE_NAME}}]" \
    && echo ${STRING_ROUTE_TABLE_TAG}

### 2.3. ルートテーブル作成

    aws ec2 create-route-table \
        --vpc-id ${VPC_ID} \
        --tag-specifications ${STRING_ROUTE_TABLE_TAG}

## 3. 確認

### 3.1. ルートテーブルの確認

ルートテーブル名[public_route_table]が表示されることを確認

    aws ec2 describe-route-tables \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=tag:Name,Values=${ROUTE_TABLE_NAME}  \
        --query 'RouteTables[].Tags[?Key == `Name`].Value' \
        --output text
