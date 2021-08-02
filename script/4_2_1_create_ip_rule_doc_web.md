<!-- omit in toc -->
# インバウンドルールの設定

## 1. 値の設定

### 1.1. 許可するプロトコル

    IP_PROTOCOL='tcp'

### 1.2. 送信元ポート

    FROM_PORT='80'

### 1.3. 送信先ポート

    TO_PORT='80'

### 1.4. 設定するIPの範囲

    IP_CIDR_RANGE='0.0.0.0/0'

### 1.5. ルールの説明

    RULE_DESCRIPTION='inbound rule (web) created by aws cli.'

### 1.6. ルール設定JSONファイル保存ディレクトリ

    DIR_INBOUND_RULE_DOC="${HOME}/environment/conf-sg"

#### 1.6.1. ディレクトリの存在確認

    ls -d ${DIR_INBOUND_RULE_DOC}

ディレクトリが存在しない（[ls: cannot access XXXXX : No such file or directory]と表示される場合
下記を実行しディレクトリ作成

    mkdir -p ${DIR_INBOUND_RULE_DOC}

### 1.7. ルール設定JSONファイル名称

    INBOUND_RULE_DOC_NAME='securitygroup-inbound-rule-web'

## 2. メイン処理

### 2.1. インバウンドルールのファイル名設定

    FILE_INBOUND_RULE_DOC="${DIR_INBOUND_RULE_DOC}/${INBOUND_RULE_DOC_NAME}.json" \
    && echo ${FILE_INBOUND_RULE_DOC}

### 2.2. インバウンドルールのJSON作成

    cat << EOF > ${FILE_INBOUND_RULE_DOC}
    [
        {
            "FromPort": ${FROM_PORT},
            "IpProtocol": "${IP_PROTOCOL}",
            "IpRanges": [
                {
                    "CidrIp": "${IP_CIDR_RANGE}",
                    "Description": "${RULE_DESCRIPTION}"
                }
            ],
            "ToPort": ${TO_PORT}
        }
    ]
    EOF
    cat ${FILE_INBOUND_RULE_DOC}

## 3. 確認

### 3.1. ファイルの存在確認

ファイルのフルパスが表示されることを確認

    ls ${FILE_INBOUND_RULE_DOC}

### 3.2. JSONフォーマットの確認

出力がないことを確認

    cat ${FILE_INBOUND_RULE_DOC} \
        |  python3 -m json.tool \
        > /dev/null
