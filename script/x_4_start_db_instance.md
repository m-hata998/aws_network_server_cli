<!-- omit in toc -->
# EC2の起動

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. EC2の名称

    EC2_TAG_NAME='db_server'

## 2. メイン処理

### 2.1. インスタンスID取得

    ARRAY_EC2_INSTANCE_IDS=$(  \
        aws ec2 describe-instances \
            --filters Name=tag-key,Values=Name \
                        Name=tag-value,Values="${EC2_TAG_NAME}" \
            --query Reservations[].Instances[].InstanceId \
            --output text
    ) && echo ${ARRAY_EC2_INSTANCE_IDS}

### 2.2. インスタンスの開始

    aws ec2 start-instances \
        --instance-ids ${ARRAY_EC2_INSTANCE_IDS}

## 3. 確認

### 3.1. インスタンスの停止確認

[running]が出力されたらOK。  
[pending]が出力される場合は時間をおいて再確認。

    aws ec2 describe-instances \
        --instance-ids ${ARRAY_EC2_INSTANCE_IDS} \
        --query 'Reservations[].Instances[].State.Name' \
        --output text
