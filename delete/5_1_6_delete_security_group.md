<!-- omit in toc -->
# セキュリティグループの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. セキュリティグループの名称

    SECURIYT_GROUP_NAME='db_sg'

### 1.4. VPCの名称

    VPC_TAG_NAME='vpc_area'

## 2. メイン処理

### 2.1. VPCID取得

    VPC_ID=$( \
        aws ec2 describe-vpcs \
            --filters Name=tag:Name,Values=${VPC_TAG_NAME}  \
            --query 'Vpcs[].VpcId' \
            --output text \
    ) && echo ${VPC_ID}

### 2.2. セキュリティグループID取得

    SECURITY_GROUP_ID=$( \
        aws ec2 describe-security-groups \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=group-name,Values=${SECURIYT_GROUP_NAME} \
            --query SecurityGroups[].GroupId \
            --output text
    ) && echo ${SECURITY_GROUP_ID}

### 2.2. セキュリティグループ削除

    aws ec2 delete-security-group \
        --group-id ${SECURITY_GROUP_ID}

## 3. 確認

### 3.1. セキュリティグループの確認

出力がないことを確認

    aws ec2 describe-security-groups \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=group-name,Values=${SECURIYT_GROUP_NAME} \
        --query SecurityGroups[].GroupName \
        --output text
