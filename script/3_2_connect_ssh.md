<!-- omit in toc -->
# SSH接続

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. キーペア名

    KEY_PAIR='my-key'

### 1.3. キーペア保存ディレクトリ

    DIR_KEY_PAIR_DOC="${HOME}/environment/ec2-conf"

### 1.4. EC2の名称

    EC2_TAG_NAME='web_server'

## 2. メイン処理

### 2.1. キーペアファイル名

    FILE_KEY_PAIR_DOC=${DIR_KEY_PAIR_DOC}/${KEY_PAIR}.pem \
    && echo ${FILE_KEY_PAIR_DOC}

#### 2.1.1. 権限設定(初回のみ)

    chmod 400 ${FILE_KEY_PAIR_DOC}

### 2.2. EC2パブリックIP取得

    EC2_PUBLIC_IP=$(  \
        aws ec2 describe-instances \
            --filters Name=tag-key,Values=Name \
                        Name=tag-value,Values="${EC2_TAG_NAME}" \
                        Name=instance-state-name,Values=running \
            --query Reservations[].Instances[].PublicIpAddress \
            --output text
    ) && echo ${EC2_PUBLIC_IP}

### 2.3. ターミナルからSSH接続

    ssh -i ${FILE_KEY_PAIR_DOC} ec2-user@${EC2_PUBLIC_IP}

Are you sure you want to continue connecting (yes/no)?  と表示されたら[yes]を入力

下記のログイン画面が表示されたらOK

        __|  __|_  )
        _|  (     /   Amazon Linux 2 AMI
        ___|\___|___|

    https://aws.amazon.com/amazon-linux-2/

## 3. 確認

特になし。
SSH接続から抜ける場合はexitを入力
