<!-- omit in toc -->
# キーペアの削除

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. キーペア名

    KEY_PAIR='my-key'

## 2. メイン処理

### 2.2. キーペア削除

    aws ec2 delete-key-pair \
        --key-name ${KEY_PAIR} 

## 3. 確認

### 3.1. キーペアファイルの存在確認

[An error occurred (InvalidKeyPair.NotFound) when calling the DescribeKeyPairs operation: The key pair 'my-key' does not exist]のエラーが出力されることを確認

    ! aws ec2 describe-key-pairs \
        --key-name ${KEY_PAIR} \
        --query KeyPairs[].KeyName \
        --output text
