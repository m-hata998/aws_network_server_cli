<!-- omit in toc -->
# インバウンドルールの設定(db)

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. セキュリティグループの名称

    SECURIYT_GROUP_NAME='web_sg'

### 1.3. VPCの名称

    VPC_TAG_NAME='vpc_area'

### 1.4. ルール設定JSONファイル保存ディレクトリ

    DIR_INBOUND_RULE_DOC="${HOME}/environment/conf-sg"

### 1.5. ルール設定JSONファイル名称

    INBOUND_RULE_DOC_NAME='securitygroup-inbound-rule-icmp'

### 1.6. プロトコル

    IP_PROTOCOL='icmp'

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

### 2.3. インバウンドルールのファイル名設定

    FILE_INBOUND_RULE_DOC="${DIR_INBOUND_RULE_DOC}/${INBOUND_RULE_DOC_NAME}.json" \
    && echo ${FILE_INBOUND_RULE_DOC}

### 2.4. DBのインバウンドルールの設定

    aws ec2 authorize-security-group-ingress \
        --group-id ${SECURITY_GROUP_ID} \
        --ip-permissions=file://${FILE_INBOUND_RULE_DOC}

## 3. 確認

### 3.1. プロトコルの確認

IPプロトコル[icmp]が表示されることを確認

    aws ec2 describe-security-groups \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=group-name,Values=${SECURIYT_GROUP_NAME} \
        --query "SecurityGroups[].IpPermissions[?IpProtocol == \`${IP_PROTOCOL}\`].IpProtocol" \
        --output text

### 3.2. 送信元ポートの確認

送信元ポート[-1]が表示されることを確認

    aws ec2 describe-security-groups \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=group-name,Values=${SECURIYT_GROUP_NAME} \
        --query "SecurityGroups[].IpPermissions[?IpProtocol == \`${IP_PROTOCOL}\`].FromPort" \
        --output text

### 3.3. 送信先ポートの確認

送信先ポート[-1]が表示されることを確認

    aws ec2 describe-security-groups \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=group-name,Values=${SECURIYT_GROUP_NAME} \
        --query "SecurityGroups[].IpPermissions[?IpProtocol == \`${IP_PROTOCOL}\`].ToPort" \
        --output text

### 3.4. CIDRの確認

表示されたJSON内に"CidrIp": "0.0.0.0/0"が表示されることを確認

    aws ec2 describe-security-groups \
        --filters Name=vpc-id,Values=${VPC_ID} \
            Name=group-name,Values=${SECURIYT_GROUP_NAME} \
        --query "SecurityGroups[].IpPermissions[?IpProtocol == \`${IP_PROTOCOL}\`].IpRanges[]"
