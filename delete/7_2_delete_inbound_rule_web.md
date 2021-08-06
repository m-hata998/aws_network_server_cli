<!-- omit in toc -->
# インバウンドルールの削除(web)

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

    INBOUND_RULE_DOC_NAME='securitygroup-inbound-rule-web'

### 1.6. 送信元ポート

    FROM_PORT='80'

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

#### 2.3.1. ファイルの存在確認

ファイルが存在しない場合は[4_2_1_create_inbound_rule_doc_web]の手順でファイルを作成

    ls ${FILE_INBOUND_RULE_DOC}

### 2.4. SSHのインバウンドルールの削除

    aws ec2 revoke-security-group-ingress \
        --group-id ${SECURITY_GROUP_ID} \
        --ip-permissions=file://${FILE_INBOUND_RULE_DOC}

## 3. 確認

### 3.1. プロトコルの確認

出力がないことを確認

    aws ec2 describe-security-groups \
            --filters Name=vpc-id,Values=${VPC_ID} \
                Name=group-name,Values=${SECURIYT_GROUP_NAME} \
            --query "SecurityGroups[].IpPermissions[?FromPort == \`${FROM_PORT}\`].IpProtocol" \
            --output text
